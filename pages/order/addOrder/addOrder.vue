<template>
	<view class="u-p-40">
		<u-form :model="form" :rules="rules" :label-width="140" ref="uForm">
			<u-form-item label="客户" prop="clientele_name">
				<u-select v-model="clienteleShow" :list="selectData.clienteles" @confirm="clienteleConfirm"></u-select>
				<u-input @click="clienteleShow = true" placeholder="请选择客户" v-model="form.clientele_name" type="select"></u-input>
			</u-form-item>
			<u-form-item label="产品" prop="product_name">
				<u-select v-model="productShow" :list="selectData.products" @confirm="productConfirm"></u-select>
				<u-input @click="productShow = true" placeholder="请输入售卖产品" v-model="form.product_name" type="select"></u-input>
			</u-form-item>
			<u-form-item label="出单量" prop="num">
				<u-input placeholder="请输入出单量" v-model="form.num" type="text"></u-input>
			</u-form-item>
			<u-form-item label="供货报价" prop="price">
				<u-input placeholder="请输入供货报价" v-model="form.price" type="text"></u-input>
			</u-form-item>
			<u-form-item label="售后单数" prop="after_num">
				<u-input placeholder="请输入售后单数" v-model="form.after_num" type="text"></u-input>
			</u-form-item>
		</u-form>
		<view class="u-p-t-30 u-p-b-30">
			<u-button @click="submit" type="primary">提交</u-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				form: {
					clientele_id: '',
					clientele_name:'',
					product_id:'',
					product_name: '',
					num:'',
					price:'',
					after_num:0
				},
		
				rules: {
					clientele_id: [{
							required: true,
							message: '请选择客户',
							
					}],
					product_id: [{
							required: true,
							message: '请选择产品',
					}],
					num: [{
							required: true,
							message: '请输入出单量',
					}],
					price: [{
							required: true,
							message: '请输入供货报价',
					}],
					after_num: [{
							required: true,
							message: '请输入售后单数',
					}],
				},
				errorType: ['toast'],
				selectData:{},
				clienteleShow:false,
				productShow:false
			};
		},
		onLoad() {
			this.getSelectData()
		},
		methods: {
			submit() {
				this.$refs.uForm.validate(valid => {
					if (valid) {
						this.$u.post('/api/v1/order/order', this.form).then(res => {
							uni.showToast({
							    title: '提交成功',
							    duration: 1600
							});
							setTimeout(function () {
							   uni.navigateBack({
							       url: '/pages/order/order'
							   });
							}, 1600);
						})
					}
				});
			},
			getSelectData(){
				this.$u.get('/api/v1/order/get_select_data').then(res => {
					this.selectData = res.data;
				})
			},
			clienteleConfirm(e) {
				this.form.clientele_id = e[0].value;
				this.form.clientele_name = e[0].label;
			},
			productConfirm(e) {
				this.form.product_id = e[0].value;
				this.form.product_name = e[0].label;
			},
		},
		onReady() {
			this.$refs.uForm.setRules(this.rules);
		}
	}
</script>

<style>

</style>
