<template>
	<view class="wrap">
		<scroll-view scroll-y style="height: 100%;width: 100%;" @scrolltolower="getProductList()">
			<view class="u-flex bg-white u-p-t-30 u-p-r-30 u-p-l-30 u-p-b-20">
				<u-button v-if="identity==2" @click="add(0)" class="u-m-r-30" size="mini">添加</u-button>
				<u-search class="u-flex-1" placeholder="请输入关键字" shape="round" :show-action="false" @change="change" v-model="keyword"></u-search>
			</view>
			<view v-if="!isEmpty" class="page-box">
				<view  v-for="(item, index) in productList" :key="item.id">
					<u-card @click="clickItem(item)" padding="0" >
						<view slot="body" class="u-size-medium">
							<view class="u-p-20">
								<u-row gutter="16">
									<u-col span="4">
										<image class="product-img-1" mode="aspectFill" :src="item.img"></image>
									</u-col>
									<u-col span="8">
										<view>
											<view class="u-font-14" style="height: 60upx;">{{item.title}}</view>
											<view class="u-flex u-p-t-10 u-p-b-10">
												<view class="u-flex-1">规格：{{item.specification}}</view>
												<view>库存：{{item.num}}</view>
											</view>
											<view class="u-flex u-p-t-10 u-p-b-10">
												<view class="u-flex-1">原价：{{item.price}}</view>
												<view>活动价：{{item.activity_price}}</view>
											</view>
										</view>
									</u-col>
								</u-row>
							</view>
						</view>
						<view  slot="foot">
							<view class="u-text-center u-p-10">
								<u-icon name="arrow-down"></u-icon>
							</view>
						</view>
					</u-card>
				</view>
				<view class="u-m-t-20 u-m-b-30">
					<u-loadmore  :status="loadStatus" bgColor="#f2f2f2"></u-loadmore>
				</view>
			</view>
			<u-empty v-if="isEmpty" mode="list"></u-empty>
		</scroll-view>
		<u-popup v-model="popupShow" mode="bottom" :closeable="true" border-radius="14">
			<view class="u-m-t-80 u-p-l-30 u-p-r-30">
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">名称</view>
					<view>{{popupData.title}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">规格</view>
					<view>{{popupData.specification}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">对接群</view>
					<view>{{popupData.group}}</view>
				</view>
				
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">统一报价</view>
					<view>{{popupData.publicity_price}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">原价</view>
					<view>{{popupData.price}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">库存</view>
					<view>{{popupData.num}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">活动价</view>
					<view>{{popupData.activity_price}}</view>
				</view>
				<view v-if="identity == 2">
					<view class="u-flex u-m-b-20">
						<view class="u-flex-1">星际供应商</view>
						<view>{{popupData.supplier}}</view>
					</view>
					<view class="u-flex u-m-b-20">
						<view class="u-flex-1">裸价</view>
						<view>{{popupData.naked_price}}</view>
					</view>
					<view class="u-flex u-m-b-20">
						<view class="u-flex-1">耗材</view>
						<view>{{popupData.consumable}}</view>
					</view>
					<view class="u-flex u-m-b-20">
						<view class="u-flex-1">运费</view>
						<view>{{popupData.carriage}}</view>
					</view>
					<view class="u-flex u-m-b-20">
						<view class="u-flex-1">编码</view>
						<view>{{popupData.id}}</view>
					</view>
					<view v-if="identity==2" class="u-m-b-20">
						<u-button @click="add(popupData.id)">修改</u-button>
					</view>
				</view>
			</view>
		</u-popup>
	</view>
</template>
<script>
export default {
	data() {
		return {
			productList: [],
			loadStatus: 'loadmore',
			page: 1,
			identity:-1,
			isEmpty:false,
			popupShow: false,
			popupData:{},
			keyword:'',
		};
	},
	onShow() {
		this.identity = uni.getStorageSync('identity');
		this.getProductList();
	},
	methods: {
		change() {
			this.page = 1;
			this.isEmpty = false;
			this.productList = [];
			this.getProductList();
		},
		getProductList() {
			this.loadStatus = 'loading';
			this.$u.get('/api/v1/product/product', {page: this.page,keyword:this.keyword}).then(res => {
				res = res.data;
				if(!res || res.total == 0){
					this.isEmpty = true;
				} else {
					if (this.page > res.last_page) {
						this.loadStatus = 'nomore';
					} else {
						this.loadStatus = 'loadmore';
						this.page = ++this.page;
						this.productList = this.productList.concat(res.data);
					}
				}
			})
		},
		clickItem(item){
			this.popupData = item;
			this.popupShow = true;
		},
		add(id){
			uni.navigateTo({
			    url: "/pages/product/add?id="+id
			});
		}
	},
};
</script>

<style>
/* #ifndef H5 */
page {
	height: 100%;
	background-color: #f2f2f2;
}
/* #endif */
</style>

<style lang="scss" scoped>
.wrap {
	display: flex;
	flex-direction: column;
	height: calc(100vh - var(--window-top));
	width: 100%;
}
.product-img-1{
	width:100%;
	height:180upx;
	border-radius: 4px;
}
</style>
