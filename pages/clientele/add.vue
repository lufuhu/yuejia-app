<template>
	<view class="u-p-40">
		<u-form :model="form" :rules="rules" :label-width="150" ref="uForm">
			<u-form-item label="客户名称" prop="name">
				<u-input placeholder="请输入客户名称" v-model="form.name" type="text"></u-input>
			</u-form-item>
			<u-form-item label="平台类型" prop="platform_att">
				<u-select v-model="platformShow" :list="platforms" @confirm="platformConfirm"></u-select>
				<u-input @click="platformShow = true" placeholder="请选择平台类型" v-model="form.platform_att" type="select"></u-input>
			</u-form-item>
			<u-form-item label="姓名" prop="contact_name">
				<u-input placeholder="请输入姓名" v-model="form.contact_name" type="text"></u-input>
			</u-form-item>
			<u-form-item label="微信" prop="contact_wx">
				<u-input placeholder="请输入微信" v-model="form.contact_wx" type="text"></u-input>
			</u-form-item>
			<u-form-item label="联系方式" prop="contact_tel">
				<u-input placeholder="请输入联系方式" v-model="form.contact_tel" type="text"></u-input>
			</u-form-item>
			<u-form-item label="职位" prop="contact_post">
				<u-input placeholder="请输入职位" v-model="form.contact_post" type="text"></u-input>
			</u-form-item>
			<u-form-item label="客户对接群" prop="group">
				<u-input placeholder="请输入客户对接群" v-model="form.group" type="text"></u-input>
			</u-form-item>
			<u-form-item label="客户星级" prop="num">
				<u-rate :count="5" v-model="form.level"></u-rate>
			</u-form-item>
			<u-form-item label="收件人姓名" prop="address_name">
				<u-input placeholder="请输入收件人姓名" v-model="form.address_name" type="text"></u-input>
			</u-form-item>
			<u-form-item label="收件人电话" prop="address_tel">
				<u-input placeholder="请输入收件人电话" v-model="form.address_tel" type="text"></u-input>
			</u-form-item>
			<u-form-item label="收件人地址" prop="address">
				<u-input placeholder="请输入收件人地址" v-model="form.address" type="text"></u-input>
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
					'name':'',
					'platform':'',
					'platform_att':'',
					'contact_name':'',
					'contact_wx':'',
					'contact_tel':'',
					'contact_post':'',
					'group':'',
					'address':'',
					'address_name':'',
					'address_tel':'',
					'level':0,
				},
		
				rules: {
					'name' :[{required: true,message: '请输入客户名称'}],
					'platform_att' :[{required: true,message: '请选择平台类型'}],
					'contact_name' :[{required: true,message: '请输入姓名'}],
					'contact_wx' :[{required: true,message: '请输入微信'}],
					'contact_tel' :[{required: true,message: '请输入联系方式'}],
				},
				errorType: ['toast'],
				platforms:[
					{label:'社群平台',value:0},
					{label:'社区平台',value:1},
					{label:'直播平台',value:2},
					{label:'供应链',value:3},
					{label:'线下经销商',value:4},
				],
				platformShow:false
			};
		},
		onLoad(option) {
			if(option.id && option.id > 0){
				this.getData(option.id);
			}
		},
		methods: {
			getData(id){
				this.$u.get('/api/v1/product/product/'+ id).then(res => {
					this.form = res.data
				})
			},
			submit() {
				this.$refs.uForm.validate(valid => {
					if (valid) {
						this.$u.post('/api/v1/clientele/clientele', this.form).then(res => {
							uni.showToast({
							    title: '提交成功',
							    duration: 1600
							});
							setTimeout(function () {
							   uni.navigateBack({
							       url: '/pages/clientele/clientele'
							   });
							}, 1600);
						})
					}
				});
			},
			platformConfirm(e) {
				this.form.platform = e[0].value;
				this.form.platform_att = e[0].label;
			},
		},
		onReady() {
			this.$refs.uForm.setRules(this.rules);
		}
	}
</script>

<style>

</style>
