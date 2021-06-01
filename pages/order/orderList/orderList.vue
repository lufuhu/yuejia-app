<template>
	<view class="wrap">
		<scroll-view scroll-y style="height: 100%;width: 100%;" @scrolltolower="getOrderList()">
			<view class="u-flex u-m-30">
				<view class="date1 u-flex u-flex-1">
					<view @click="showDate(1)" class="u-flex-1">{{start_date}}</view>
					|
					<view @click="showDate(2)" class="u-flex-1">{{end_date}}</view>
				</view>
				<u-button size="mini" class="u-m-l-10" @click="onSearch()">搜索</u-button>
			</view>
			<view v-if="!isEmpty" class="page-box">
				<view v-for="(item, index) in orderList" :key="item.id">
					<u-card :thumb="item.product.img" @head-click="onCheck(item)" :thumb-circle="true" :title="item.product.title" :sub-title="item.status_att" padding="20" >
						<view slot="body" class="u-size-medium"> 
							<u-row gutter="16">
								<u-col span="12">
									<view class="u-flex u-p-t-10 u-p-b-10">
										<view class="label-1">客户</view>
										<view>{{item.clientele.name}}</view>
									</view>
								</u-col>
							</u-row>
							<u-row gutter="16">
								<u-col span="6">
									<view class="u-flex u-p-t-10 u-p-b-10">
										<view class="label-1">每单成本</view>
										<view>{{item.price}}</view>
									</view>
								</u-col>
								<u-col span="6">
									<view class="u-flex u-p-t-10 u-p-b-10">
										<view class="label-1">供货报价</view>
										<view>{{item.price}}</view>
									</view>
								</u-col>
							</u-row>
							<u-row gutter="16">
								<u-col span="6">
									<view class="u-flex u-p-t-10 u-p-b-10">
										<view class="label-1">出单量</view>
										<view>{{item.num}}</view>
									</view>
								</u-col>
								<u-col span="6">
									<view class="u-flex u-p-t-10 u-p-b-10">
										<view class="label-1">售后单数</view>
										<view>{{item.after_num}}</view>
									</view>
								</u-col>
							</u-row>
							<u-row v-if="identity == 2" gutter="16">
								<u-col span="6">
									<view class="u-flex u-p-t-10 u-p-b-10">
										<view class="label-1">每单成本</view>
										<view>{{item.product.publicity_price}}</view>
									</view>
								</u-col>
								<u-col span="6">
									<view class="u-flex u-p-t-10 u-p-b-10">
										<view class="label-1">出单人</view>
										<view>{{item.user.nickname}}</view>
									</view>
								</u-col>
							</u-row>
						</view>
						<view  slot="foot">
							<view class="u-flex">
								<view class="u-flex-1">
									{{item.created_at}}
								</view>
								<view>
									合计：{{( (item.num - item.after_num) * item.price ).toFixed(2)}}
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
		<u-picker mode="time" v-model="show" :params="dateArr" @confirm="dateConfirm"></u-picker>
		<u-modal v-model="showModel" title="更新订单状态" :show-cancel-button="true" @confirm="onConfirm()" :async-close="true">
			<view class="u-p-30">
				<view class="u-m-b-30 u-text-center u-m-t-10">
					<u-radio-group v-model="form.status">
						<u-radio name="0">未收款</u-radio>
						<u-radio name="1">已收款</u-radio>
						<u-radio name="7">撤销订单</u-radio>
					</u-radio-group>
				</view>
			</view>
		</u-modal>
	</view>
</template>

<script>
export default {
	data() {
		return {
			orderList: [],
			loadStatus: 'loadmore',
			page: 1,
			identity:-1,
			isEmpty:false,
			start_date:'',
			end_date:'',
			dateArr: {
				year: true,
				month: true,
				day: true,
				hour: false,
				minute: false,
				second: false
			},
			show: false,
			dateIndex:0,
			showModel:false,
			form:{
				id:'',
				status:0,
			},
		};
	},
	onLoad() {
		this.start_date = new Date().Format("yyyy-MM-01");
		this.end_date = new Date().Format("yyyy-MM-dd");
		this.identity = uni.getStorageSync('identity');
		this.getOrderList();
	},
	methods: {
		onCheck(item){
			this.form = {
				id:item.id,
				status:item.status,
			};
			this.showModel = true;
		},
		onConfirm(){
			this.$u.post('/api/v1/order/order/'+this.form.id, this.form).then(res => {
				this.showModel = false;
				uni.showToast({
				    title: "操作成功",
					icon:'none'
				});
				this.page = 1;
				this.isEmpty = false;
				this.orderList = [];
				this.getOrderList();
			})
		},
		dateConfirm(val){
			let date = val.year + '-' + val.month + '-' + val.day;
			if(this.dateIndex == 1){
				this.start_date = date
			} else if (this.dateIndex == 2){
				this.end_date = date
			}
		},
		showDate(index){
			this.dateIndex = index;
			this.show = true;
		},
		onSearch(){
			this.page = 1;
			this.isEmpty = false;
			this.orderList = [];
			this.getOrderList();
		},
		getOrderList() {
			this.loadStatus = 'loading';
			this.$u.get('/api/v1/order/order', {page: this.page,start_date:this.start_date,end_date:this.end_date}).then(res => {
				res = res.data;
				if(!res || res.total == 0){
					this.isEmpty = true;
				} else {
					if (this.page > res.last_page) {
						this.loadStatus = 'nomore';
					} else {
						this.loadStatus = 'loadmore';
						this.page = ++this.page;
						this.orderList = this.orderList.concat(res.data);
					}
				}
			})
		},
	}
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
	width: 120upx;
}
.product-img{
	width:50upx;
	height:50upx;
	border-radius :50%;
}
.date1{
	background-color: #ffffff;
	border-radius: 1rem;
	height: 1.5rem;
	margin: 0 0.25rem;
	color: #C0C4CC;
}
.date1 .u-flex-1{
	width: 100%;
	height:100%;
	line-height: 1.5rem;
	text-align: center;
}
</style>
