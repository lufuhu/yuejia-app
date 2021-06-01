<template>
	<view>
		<view class="u-text-center user-box u-p-t-50 u-p-b-50">
			<view>
				<image class="user-avatar" :src="userInfo.avatarurl"></image>
			</view>
			<view class="u-p-t-20">
				<view class="u-font-14 u-p-b-20">{{userInfo.nickname}}</view>
				<view class="u-font-12 u-tips-color">{{identityData[userInfo.identity]}}</view>
			</view>
		</view>
		
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item v-if="userInfo.check_user == 1" @click="clickCellItem('/pages/user/checkUser')" icon="checkmark-circle" title="审核成员"></u-cell-item>
				<u-cell-item v-if="userInfo.check_user == 1" @click="clickCellItem('/pages/user/productStore')" icon="error-circle" title="库存记录"></u-cell-item>
			</u-cell-group>
		</view>
		
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item @click="clickCellItem('/pages/user/bindPhone')" icon="phone" title="手机号" :value="phonePrivary"></u-cell-item>
				<u-cell-item @click="clickCellItem('/pages/user/bindMail')" icon="email" title="邮箱" :value="mailPrivary"></u-cell-item>
			</u-cell-group>
		</view>
		
<!-- 		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item icon="setting" title="预留"></u-cell-item>
			</u-cell-group>
		</view> -->
		
		<view class="u-p-20 u-m-t-30 u-m-b-30">
			<u-button @click="onLoginout()" type="error">退出</u-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userInfo:{},
				identity:-1,
				identityData: {0:'未审核', 1:'员工', 2:'管理员'},
				phonePrivary:'绑定',
				mailPrivary:"绑定",
			}
		},
		methods: {
			onLoginout(){
				this.$u.post('/api/v1/auth/loginout').then(res => {
					uni.clearStorage();
					uni.reLaunch({
					    url: '/pages/login/login'
					});
				})
			},
			clickCellItem(url){
				uni.navigateTo({
				    url: url
				});
			}
		},
		onShow() {
			this.userInfo = uni.getStorageSync('userInfo');
			if(this.userInfo && this.userInfo.phone){
				this.phonePrivary = this.userInfo.phone.replace(/(\d{3})\d{4}(\d{4})/, '$1****$2')
			}
			if(this.userInfo && this.userInfo.mail){
				this.mailPrivary = this.userInfo.mail
			}
		},
	}
</script>

<style lang="scss">
page{
	background-color: #ededed;
}

.camera{
	width: 54px;
	height: 44px;
	
	&:active{
		background-color: #ededed;
	}
}
.user-box{
	background-color: #fff;
}
.user-avatar{
	width:150rpx;
	height:150rpx;
	border-radius: 50%;
}
</style>
