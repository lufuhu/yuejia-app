<template>
	<view>
		<view class="u-m-b-30">
			<u-notice-bar :list="noticeList"></u-notice-bar>
		</view>
		<view v-if="identity == 1">
			<u-card @head-click="toOrderList()" title="出单" sub-title="查看记录" padding="20" >
				<view class="u-text-center u-p-t-50 u-p-b-50" slot="body" > 
					<u-button @click="addOrder()" size="medium">出单</u-button>
				</view>
			</u-card>
			<u-card @head-click="toSampleList()" title="寄样" sub-title="查看记录" padding="20">
				<view class="u-text-center u-p-t-50 u-p-b-50" slot="body" > 
					<u-button @click="addSample()" size="medium">寄样</u-button>
				</view>
			</u-card>
		</view>
		<view v-if="identity == 2">
			<u-row gutter="0">
				<u-col span="3" >
					<view class="u-text-center">
						<u-button 
						:type="orderItemIndex === 0 ? 'error' : 'default'" 
						@click="clickOrderItem(0)" 
						size="mini" plain>
							<view class="order-item-but" >日</view>
						</u-button>
					</view>
				</u-col>
				<u-col span="3">
					<view class="u-text-center">
						<u-button
						:type="orderItemIndex === 1 ? 'error' : 'default'" 
						@click="clickOrderItem(1)" 
						size="mini" plain>
							<view class="order-item-but" >月</view>
						</u-button>
					</view>
				</u-col>
				<u-col span="3">
					<view class="u-text-center">
						<u-button
						:type="orderItemIndex === 2 ? 'error' : 'default'" 
						@click="clickOrderItem(2)" 
						size="mini" plain>
							<view class="order-item-but" >季</view>
						</u-button>
					</view>
				</u-col>
				<u-col span="3">
					<view class="u-text-center">
						<u-button
						:type="orderItemIndex === 3 ? 'error' : 'default'" 
						@click="clickOrderItem(3)" 
						size="mini" plain>
							<view class="order-item-but" >年</view>
						</u-button>
					</view>
				</u-col>
			</u-row>
			
			<u-card title="销量统计" padding="20">
				<view slot="body" >
					<view class="u-p-t-50 u-p-b-50">
						<view v-if="orderItemIndex === 0">
							<u-row gutter="0">
								<u-col span="6">
									<view class="u-text-center">
										<u-count-to 
										:start-val="0" 
										:end-val="countData.day.turnover" 
										:duration="1000"
										:decimals="2"
										separator=","
										></u-count-to>
										<view class="u-m-t-20">日营业额</view>
									</view>
								</u-col>
								<u-col span="6">
									<view class="u-text-center">
										<u-count-to
										:start-val="0" 
										:end-val="countData.day.profit" 
										:duration="1000"
										:decimals="2"
										separator=","
										></u-count-to>
										<view class="u-m-t-20">月利润</view>
									</view>
								</u-col>
							</u-row>
						</view>
						<view v-if="orderItemIndex === 1" >
							<u-row gutter="0">
								<u-col span="6">
									<view class="u-text-center">
										<u-count-to
										:start-val="0" 
										:end-val="countData.month.turnover" 
										:duration="1000"
										:decimals="2"
										separator=","
										></u-count-to>
										<view class="u-m-t-20">月营业额</view>
									</view>
								</u-col>
								<u-col span="6">
									<view class="u-text-center">
										<u-count-to
										:start-val="0" 
										:end-val="countData.month.profit" 
										:duration="1000"
										:decimals="2"
										separator=","
										></u-count-to>
										<view class="u-m-t-20">月利润</view>
									</view>
								</u-col>
							</u-row>
						</view>
						<view v-if="orderItemIndex === 2">
							<u-row gutter="0">
								<u-col span="6" >
									<view class="u-text-center">
										<u-count-to
										:start-val="0" 
										:end-val="countData.quarter.turnover" 
										:duration="1000"
										:decimals="2"
										separator=","
										></u-count-to>
										<view class="u-m-t-20">季度营业额</view>
									</view>
								</u-col>
								<u-col span="6" >
									<view class="u-text-center">
										<u-count-to
										:start-val="0" 
										:end-val="countData.quarter.profit" 
										:duration="1000"
										:decimals="2"
										separator=","
										></u-count-to>
										<view class="u-m-t-20">季度利润</view>
									</view>
								</u-col>
							</u-row>
						</view>
						<view v-if="orderItemIndex === 3">
							<u-row gutter="0">
								<u-col span="6" >
									<view class="u-text-center">
										<u-count-to
										:start-val="0" 
										:end-val="countData.year.turnover" 
										:duration="1000"
										:decimals="2"
										separator=","
										></u-count-to>
										<view class="u-m-t-20">年营业额</view>
									</view>
									
								</u-col>
								<u-col span="6" >
									<view class="u-text-center">
										<u-count-to
										:start-val="0" 
										:end-val="countData.year.profit" 
										:duration="1000"
										:decimals="2"
										separator=","
										></u-count-to>
										<view class="u-m-t-20">年利润</view>
									</view>
								</u-col>
							</u-row>
						</view>
					</view>
					
				</view>
			</u-card>
			
			<u-card @head-click="toOrderList()" title="出单统计" sub-title="查看记录" padding="20">
				<view slot="body" >
					<view class="u-p-t-50 u-p-b-50">
						<view v-if="orderItemIndex === 0" class="u-text-center">
							<u-avatar size="150" :text="countData.day.order_num" bgColor='#fcbd71'></u-avatar>
							<view class="u-m-t-20">日出单量</view>
						</view>
						<view v-if="orderItemIndex === 1" >
							<u-row gutter="0">
								<u-col span="6">
									<view class="u-text-center">
										<u-avatar size="150" :text="countData.month.order_num" bgColor='#fcbd71'></u-avatar>
										<view class="u-m-t-20">月出单量</view>
									</view>
								</u-col>
								<u-col span="6">
									<view class="u-text-center">
										<u-avatar size="150" :text="countData.month.order_daily_num" bgColor='#fcbd71'></u-avatar>
										<view class="u-m-t-20">月平均日均单量</view>
									</view>
								</u-col>
							</u-row>
						</view>
						<view v-if="orderItemIndex === 2">
							<u-row gutter="0">
								<u-col span="6" >
									<view class="u-text-center">
										<u-avatar size="150" :text="countData.quarter.order_num" bgColor='#fcbd71'></u-avatar>
										<view class="u-m-t-20">季度出单量</view>
									</view>
								</u-col>
								<u-col span="6" >
									<view class="u-text-center">
										<u-avatar size="150" :text="countData.quarter.order_daily_num" bgColor='#fcbd71'></u-avatar>
										<view class="u-m-t-20">季度日均单量</view>
									</view>
								</u-col>
							</u-row>
						</view>
						<view v-if="orderItemIndex === 3">
							<u-row gutter="0">
								<u-col span="6" >
									<view class="u-text-center">
										<u-avatar size="150" :text="countData.year.order_num" bgColor='#fcbd71'></u-avatar>
										<view class="u-m-t-20">年度出单量</view>
									</view>
									
								</u-col>
								<u-col span="6" >
									<view class="u-text-center">
										<u-avatar size="150" :text="countData.year.order_daily_num" bgColor='#fcbd71'></u-avatar>
										<view class="u-m-t-20">年度日均单量</view>
									</view>
								</u-col>
							</u-row>
						</view>
					</view>
					
				</view>
			</u-card>
			
			<u-card @head-click="toSampleList()" title="寄样统计" sub-title="查看记录" padding="20">
				<view slot="body" >
					<view class="u-p-t-50 u-p-b-50">
						<view v-if="orderItemIndex === 0" class="u-text-center">
							<u-avatar size="150" :text="countData.day.sample_num" bgColor='#fcbd71'></u-avatar>
							<view class="u-m-t-20">日寄样数</view>
						</view>
						<view v-if="orderItemIndex === 1" class="u-text-center">
							<u-avatar size="150" :text="countData.month.sample_num" bgColor='#fcbd71'></u-avatar>
							<view class="u-m-t-20">月寄样数</view>
						</view>
						<view v-if="orderItemIndex === 2" class="u-text-center">
							<u-avatar size="150" :text="countData.quarter.sample_num" bgColor='#fcbd71'></u-avatar>
							<view class="u-m-t-20">季寄样数</view>
						</view>
						<view v-if="orderItemIndex === 3" class="u-text-center">
							<u-avatar size="150" :text="countData.year.sample_num" bgColor='#fcbd71'></u-avatar>
							<view class="u-m-t-20">年寄样数</view>
						</view>
					</view>
					
				</view>
			</u-card>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				countData:{},
				noticeList: [
					'悦家丨叭滋食品内部系统',
					'出单、寄样请注意信息填写，如有错误，请及时联系管理员更改',
					'订单提交成功后，管理员会在3个工作日内处理。'
				],
				identity:-1,
				orderItemIndex:0
			}
		},
		onLoad() {
			this.getData();
		},
		methods: {
			getData(){
				let identity = uni.getStorageSync('identity');
				if (identity == 2){
					this.$u.get('/api/v1/order/statistics').then(res => {
						this.countData = res.data
						this.identity = identity
					})
				} else {
					this.identity = identity
				}
			},
			clickOrderItem(index){
				this.orderItemIndex = index
			},
			addOrder(){
				uni.navigateTo({
				    url: 'addOrder/addOrder'
				});
			},
			addSample(){
				uni.navigateTo({
				    url: 'addSample/addSample'
				});
			},
			toOrderList(){
				uni.navigateTo({
				    url: 'orderList/orderList'
				});
			},
			toSampleList(){
				uni.navigateTo({
				    url: 'sampleList/sampleList'
				});
			}
		}
	}
</script>

<style scoped>
	.order-item-but{
		width:50rpx;
	}
</style>
