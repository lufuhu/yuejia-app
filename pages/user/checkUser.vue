<template>
	<view class="wrap">
		<scroll-view scroll-y style="height: 100%;width: 100%;" @scrolltolower="getUserIidentityList()">
			<view v-if="!isEmpty" class="page-box">
				<view  v-for="(item, index) in userIidentityList" :key="item.id">
					<u-card @click="clickItem(item.id)" padding="0" >
						<view slot="body" class="u-size-medium">
							<view class="u-p-20 u-flex">
								<view class="u-p-r-20">
									<u-avatar :src="item.user.avatarurl"></u-avatar>
								</view>
								<view class="u-flex-1">
									<view class="u-m-b-10 u-flex">
										<view class="u-font-16 u-m-r-20">{{item.user.nickname}}</view>
										<u-tag :text="item.identity_att" size="mini" mode="plain" type="error" shape="circle" />
									</view>
									<view v-if="item.remark">
										{{item.remark}}
									</view>
								</view>
								<view class="u-flex-none">
									<view v-if="item.status == 0">
										<u-button @click="onCheck(item.id)" size="mini">审核</u-button>
									</view>
									<view v-else>
										{{item.status_att}}
									</view>
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
		<u-modal v-model="show" title="审核" :show-cancel-button="true" @confirm="onConfirm()" :async-close="true">
			<view class="u-p-30">
				<view class="u-m-b-30">
					<u-radio-group v-model="form.status">
						<u-radio name="1">通过</u-radio>
						<u-radio name="2">拒绝</u-radio>
					</u-radio-group>
				</view>
				<u-input class="u-font-12" v-model="form.remark" type="textarea" placeholder="备注" border="true" height="100" />
			</view>
		</u-modal>
	</view>
</template>
<script>
export default {
	data() {
		return {
			userIidentityList: [],
			loadStatus: 'loadmore',
			page: 1,
			isEmpty:false,
			show:false,
			form:{
				id:'',
				status:0,
				remark:'',
			},
		};
	},
	onShow() {
		this.getUserIidentityList();
	},
	methods: {
		getUserIidentityList() {
			this.loadStatus = 'loading';
			this.$u.get('/api/v1/auth/user_identity', {page: this.page}).then(res => {
				res = res.data;
				if(!res || res.total == 0){
					this.isEmpty = true;
				} else {
					if (this.page > res.last_page) {
						this.loadStatus = 'nomore';
					} else {
						this.loadStatus = 'loadmore';
						this.page = ++this.page;
						this.userIidentityList = this.userIidentityList.concat(res.data);
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
		onCheck(id){
			this.form = {
				id:id,
				status:0,
				remark:'',
			};
			this.show = true;
		},
		onConfirm(){
			this.$u.post('/api/v1/auth/user_identity/'+this.form.id, this.form).then(res => {
				this.show = false;
				uni.showToast({
				    title: "审核成功",
					icon:'none'
				});
				this.page = 1;
				this.userIidentityList = [];
				this.getUserIidentityList();
			})
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
