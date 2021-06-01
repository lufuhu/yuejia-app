<template>
	<view class="u-bg-white">
		<view class="u-p-30">
			<u-form :label-width="100">
				<u-form-item label="手机号">
					<u-input placeholder="请输入手机号" v-model="form.phone" type="text"></u-input>
				</u-form-item>
				<u-form-item label="验证码">
					<view class="u-flex">
						<u-input class="u-flex-1" placeholder="请输入验证码" v-model="form.code" type="text"></u-input>
						<u-verification-code ref="uCode" @change="codeChange"></u-verification-code>
						<view class="primary-color" @click="getCode()">{{tips}}</view>
					</view>
				</u-form-item>
			</u-form>
			<view class="u-p-t-50 u-p-b-50">
				<u-button @click="submit" type="error">绑定手机号</u-button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				form: {
					phone:'',
					code:'',
				},
				tips:'',
			}
		},
		methods: {
			submit() {
				if(!this.form.phone){
					uni.showToast({
					    title: "请输入手机号",
						icon:'none'
					});
					return;
				} else if(!this.form.code){
					uni.showToast({
					    title: "请输入验证码",
						icon:'none'
					});
					return;
				}
				this.$u.post('/api/v1/auth/bind_phone', this.form).then(res => {
					uni.setStorageSync('access_token', res.data.token);
					uni.setStorageSync('userInfo', res.data.userInfo);
					uni.navigateBack({
					    url: '/pages/user/user'
					});
				})
			},
			codeChange(text) {
				this.tips = text;
			},
			getCode() {
				if(this.$refs.uCode.canGetCode) {
					if(!this.form.phone){
						uni.showToast({
						    title: "请输入手机号",
							icon:'none'
						});
						return;
					}
					this.$u.post('/api/v1/auth/phone_code', this.form).then(res => {
						uni.showToast({
						    title: res.message,
							icon:'none',
							duration:5000
						});
						this.$refs.uCode.start();
					})
				} else {
					this.$u.toast('倒计时结束后再发送');
				}
			},
		}
	}
</script>
<style scoped>
	.logo-lg{
		width: 80vw;
		margin-top: -80upx;
	}
	.form-input{
		background-color: #ffffff;
		border-radius: 20upx;
		padding: 10upx 30upx;
		margin-bottom: 30upx;
	}
</style>
