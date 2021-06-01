<template>
	<view class="wrap u-text-center">
		<view class="mt-30">
			<image class="user-avatar" :src="avatarurl"></image>
		</view>
		<view class="u-m-t-10">{{nickname}}</view>
		<view >
			<view class="u-m-t-80 u-p-t-30">
				<u-row gutter="50" >
					<u-col span="6">
						<view class="u-text-right">
							<u-button @click="onLogin(1)" size="medium">团队成员</u-button>
							</view>
					</u-col>
					<u-col span="6">
						<view class="u-text-left">
							<u-button @click="onLogin(2)" size="medium">管理员</u-button>
							</view>
					</u-col>
				</u-row>
			</view>
		</view>
		<u-modal v-model="show" @confirm="addTeam()" content="非悦家成员,是否申请加入？" :show-cancel-button="true"></u-modal>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userInfo:{},
				show: false,
				form:{
					identity:0
				},
				avatarurl:'',
				nickname:'',
				userForm:{
					code:'',
					userInfo:{},
					identity:0,
				}
			}
		},
		mounted() {
		},
		methods: {	
			doLogin(res, identity){
				
				try {
				    uni.clearStorageSync();
				} catch (e) {
				    // error
				}
				uni.setStorageSync('access_token', res.token);
				uni.setStorageSync('userInfo', res.userInfo);
				if(res.userInfo.identity == 0){
					this.form.identity = identity
					this.show = true;
					return;
				}
				if(res.userInfo.identity == 1 && identity == 2){
					uni.showToast({
					    title: "账号类型错误",
					    duration: 2000,
						icon:'none'
					});
					return;
				}
				uni.setStorageSync('identity', identity);
				uni.showToast({
				    title: "登录成功",
				    duration: 2000,
					icon:'none'
				});
				uni.switchTab({
				    url: '/pages/order/order'
				});
			},
			onLogin (identity){
				this.userForm.identity = identity;
				let that = this;
				// #ifdef H5
				that.$u.post('/api/v1/auth/test_login').then(res => {
					that.doLogin(res.data, identity);
				})
				// #endif
				// #ifdef MP-WEIXIN
				uni.getUserProfile({
				   desc:'Wexin',
				   success:infoRes=>{
					   that.$u.post('/api/v1/auth/wx_login', {userInfo:infoRes.userInfo,identity:identity}).then(res => {
						that.doLogin(res.data, identity);
					   })
				   },
				   fail:err=>{
					   uni.showToast({
						   title: "授权失败",
						icon:'none'
					   });
				   }
				})
				// #endif
			},
			addTeam(){
				this.$u.post('/api/v1/auth/user_identity', this.form).then(res => {
					uni.showToast({
					    title: "申请已提交",
					    duration: 2000,
						icon:'none'
					});
				})
			}
		},
	}
</script>

<style lang="scss" scoped>
.wrap {
	display: flex;
	flex-direction: column;
	height: calc(100vh - var(--window-top));
	width: 100%;
}
.mt-30{
	margin-top: 25vh;
}
.user-avatar{
	width:150rpx;
	height:150rpx;
	border-radius: 50%;
}
</style>
