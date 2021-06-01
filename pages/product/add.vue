<template>
	<view class="u-p-40">
		<u-form :model="form" :rules="rules" :label-width="150" ref="uForm">
			<u-form-item label="名称" prop="title">
				<u-input placeholder="请输入名称" v-model="form.title" type="text"></u-input>
			</u-form-item>
			<u-form-item label="规格" prop="specification">
				<u-input placeholder="请输入规格" v-model="form.specification" type="text"></u-input>
			</u-form-item>
			<u-form-item label="对接群名称" prop="group">
				<u-input placeholder="请输入对接群名称" v-model="form.group" type="text"></u-input>
			</u-form-item>
			<u-form-item label="星际供应商" prop="supplier">
				<u-input placeholder="请输入星际供应商" v-model="form.supplier" type="text"></u-input>
			</u-form-item>
			<u-form-item label="统一报价" prop="publicity_price">
				<u-input placeholder="请输入统一报价" v-model="form.publicity_price" type="number"></u-input>
			</u-form-item>
			<u-form-item label="原价" prop="price">
				<u-input placeholder="请输入原价" v-model="form.price" type="number"></u-input>
			</u-form-item>
			<u-form-item label="展示图" prop="img">
				<u-upload ref="uUpload" action="https://yj.api.lufuhu.com/api/v1/index/upload" 
				:file-list="fileList" @on-success="uploadSuccess" max-count="1"></u-upload>
			</u-form-item>
			<u-form-item label="活动价" prop="activity_price">
				<u-input placeholder="请输入活动价" v-model="form.activity_price" type="number"></u-input>
			</u-form-item>
			<u-form-item label="裸价" prop="naked_price">
				<u-input placeholder="请输入裸价" v-model="form.naked_price" type="number"></u-input>
			</u-form-item>
			<u-form-item label="耗材" prop="consumable">
				<u-input placeholder="请输入耗材" v-model="form.consumable" type="number"></u-input>
			</u-form-item>
			<u-form-item label="运费" prop="carriage">
				<u-input placeholder="请输入运费" v-model="form.carriage" type="number"></u-input>
			</u-form-item>

		</u-form>
		<view class="u-p-t-30 u-p-b-30">
			<u-button @click="submit"  type="primary">提交</u-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				form: {
					'title':'',
					'specification':'',
					'group':'',
					'supplier':'',
					'price':'',
					'activity_price':0,
					'img':'', 
					'naked_price':0,
					'consumable':0,
					'carriage':0,
					'publicity_price':'',
				},
				rules: {
					'title' :[{required: true,message: '请输入名称'}],
					'specification' :[{required: true,message: '请输入规格'}],
					'group' :[{required: true,message: '请输入对接群名称'}],
					'supplier' :[{required: true,message: '请输入星际供应商'}],
					'naked_price' :[{required: true,message: '请输入裸价'}],
					'consumable' :[{required: true,message: '请输入耗材'}],
					'carriage' :[{required: true,message: '请输入运费'}],
					'publicity_price' :[{required: true,message: '请输入统一报价'}],
					'price' :[{required: true,message: '请输入原价'}],
					'activity_price' :[{required: true,message: '请输入活动价'}],
					'img' :[{required: true,message: '请上传展示图'}],
				},
				errorType: ['toast'],
				fileList: []
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
					this.fileList = [this.form.img]
				})
			},
			submit() {
				this.$refs.uForm.validate(valid => {
					if (valid) {
						this.$u.post('/api/v1/product/product', this.form).then(res => {
							uni.showToast({
							    title: '添加成功',
							    duration: 1600
							});
							setTimeout(function () {
							   uni.navigateBack({
							       url: '/pages/product/product'
							   });
							}, 1600);
						})
					}
				});
			},
			uploadSuccess(data,index,list,name){
				if(data.status == 1){
					this.form.img = data.data
					uni.showToast({
					    title: '上传成功',
					});
				} else {
					uni.showToast({
					    title: '上传失败',
						icon:'none'
					});
				}
			}
		},
		onReady() {
			this.$refs.uForm.setRules(this.rules);
		}
	}
</script>

<style>

</style>
