<template>
	<view class="wrap">
		<scroll-view scroll-y style="height: 100%;width: 100%;" @scrolltolower="getList()">
			<view v-if="!isEmpty" class="page-box">
				<view  v-for="(item, index) in list" :key="item.id">
					<u-card :thumb="item.product.img" :thumb-circle="true" :sub-title="item.created_at" :title="item.product.title">
						<view slot="body" class="u-size-medium">
							<view>{{item.remark}}</view>
						</view>
						<view  slot="foot">
							<view class="u-flex u-m-t-30">
								<view class="u-flex-1 u-flex">
									<view>更新前：</view>
									<view>{{item.before_num}}</view>
								</view>
								<view class="u-flex-1 u-flex">
									<view>变更：</view>
									<view>{{item.num}}</view>
								</view>
								<view class="u-flex-1 u-flex">
									<view>更新后：</view>
									<view>{{item.after_num}}</view>
								</view>
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
	</view>
</template>
<script>
export default {
	data() {
		return {
			list: [],
			loadStatus: 'loadmore',
			page: 1,
			isEmpty:false,
		};
	},
	onShow() {
		this.getList();
	},
	methods: {
		getList() {
			this.loadStatus = 'loading';
			this.$u.get('/api/v1/product/product_store', {page: this.page}).then(res => {
				res = res.data;
				if(!res || res.total == 0){
					this.isEmpty = true;
				} else {
					if (this.page > res.last_page) {
						this.loadStatus = 'nomore';
					} else {
						this.loadStatus = 'loadmore';
						this.page = ++this.page;
						this.list = this.list.concat(res.data);
					}
				}
			})
		},
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
