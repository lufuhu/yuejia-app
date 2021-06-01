<template>
	<view class="wrap">
		<scroll-view scroll-y style="height: 100%;width: 100%;" @scrolltolower="getClienteleList()">
			<view class="u-flex bg-white u-p-t-30 u-p-r-30 u-p-l-30 u-p-b-20">
				<u-button v-if="identity==2" @click="add(0)" class="u-m-r-30" size="mini">添加</u-button>
				<u-search class="u-flex-1" placeholder="请输入关键字" :show-action="false" shape="round" @change="change" v-model="keyword"></u-search>
			</view>
			<view v-if="!isEmpty" class="page-box">
				<view  v-for="(item, index) in clienteleList" :key="item.id">
					<u-card @click="clickItem(item)" padding="0" >
						<view slot="body" class="u-size-medium">
							<view class="u-p-20">
								<view class="u-flex u-p-t-10 u-p-b-10">
									<view class="label-1">客户：</view>
									<view>{{item.name}}</view>
								</view>
								<view class="u-flex u-p-t-10 u-p-b-10">
									<view class="label-1">对接群：</view>
									<view>{{item.group}}</view>
								</view>
							</view>
						</view>
						<view v-if="identity ==  2" slot="foot">
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
					<view class="u-flex-1">客户名称</view>
					<view>{{popupData.name}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">平台类型</view>
					<view>{{popupData.platform}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">客户星级</view>
					<view><u-rate :count="5" v-model="popupData.level"></u-rate></view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">对接人姓名</view>
					<view>{{popupData.contact_name}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">对接人微信</view>
					<view>{{popupData.contact_wx}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">对接人电话</view>
					<view>{{popupData.contact_tel}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">对接人职位</view>
					<view>{{popupData.contact_post}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">客户对接群</view>
					<view>{{popupData.group}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">收件人姓名</view>
					<view>{{popupData.address_name}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">收件人电话</view>
					<view>{{popupData.address_tel}}</view>
				</view>
				<view class="u-flex u-m-b-20">
					<view class="u-flex-1">收件人地址</view>
					<view style="max-width: 70%;">{{popupData.address}}</view>
				</view>
				<view v-if="identity==2" class="u-m-b-20">
					<u-button @click="add(popupData.id)">修改</u-button>
				</view>
			</view>
		</u-popup>
	</view>
</template>
<script>
export default {
	data() {
		return {
			clienteleList: [],
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
		this.getClienteleList();
	},
	methods: {
		change() {
			this.page = 1;
			this.isEmpty = false;
			this.clienteleList = [];
			this.getClienteleList();
		},
		getClienteleList() {
			this.loadStatus = 'loading';
			this.$u.get('/api/v1/clientele/clientele', {page: this.page, keyword:this.keyword}).then(res => {
				res = res.data;
				if(!res || res.total == 0){
					this.isEmpty = true;
				} else {
					if (this.page > res.last_page) {
						this.loadStatus = 'nomore';
					} else {
						this.loadStatus = 'loadmore';
						this.page = ++this.page;
						this.clienteleList = this.clienteleList.concat(res.data);
					}
				}
			})
		},
		clickItem(item){
			if(this.identity == 2){
				this.popupData = item;
				this.popupShow = true;
			}
		},
		add(id){
			uni.navigateTo({
			    url: "/pages/clientele/add?id="+id
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
.label-1{
	width: 110upx;
}
.product-img-1{
	width:100%;
	height:180upx;
	border-radius: 4px;
}

</style>
