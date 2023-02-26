<template>
	<view class="container">
		<view class="bg"></view>
		<view class="form">
			<view class="header">
				<view class="title">会员权益</view>
			</view>
			<u-grid :border="true" >
				<u-grid-item v-for="(baseListItem,baseListIndex) in baseList" :key="baseListIndex" >
					<u-icon
							:customStyle="{paddingTop:20+'rpx'}"
							:name="baseListItem.name"
							:size="32"
							:color="baseListItem.color" 
					></u-icon>
					<text class="grid-text">{{baseListItem.title}}</text>
				</u-grid-item>
			</u-grid>
			<u-toast ref="uToast" />
			
			<view style="width:100%;display:flex; justify-content: space-evenly;margin-top: 80px;">
				<view v-for="(item, index) in productList" :key="index"  class="product" :class="active==item.id ? 'activeClass1' : 'activeClass2'" @tap="xuanzhong(item.id)">
					<view>{{item.name}}</view>
					<view style="font-weight: bold;">￥{{item.now_price}}</view>
					<view v-if="item.type == 2" style="text-decoration:line-through">￥{{item.price}}</view>
				</view>
				
			</view>
			<u-alert title="温馨提示!" type = "warning" :description="desc"></u-alert>
			
			<view style="margin-top: 40rpx;">
				<u-button @tap="pay" type="primary" size="middle" text="立即支付" color="#3cb034"></u-button>
			</view>
			
			<view class="share">
				<u-button shape="circle" open-type="share" color="#26B3A0" :plain="true" icon="share" text="推荐给朋友"></u-button>
			</view>
		</view>
	</view>
</template>

<script>
	let _this = null;
	export default {
		data() {
			return {
				desc:"会员服务为虚拟商品，支付成功后不支持退款. \n 邀请一位好友使用 立享【3折】优惠",
				active: -1,
				productList: [],
				baseList: [
					{
						name: 'integral-fill',
						title: '无限对话',
						color: 'gold',
					},
					{
						name: 'server-man',
						title: '1对1客服',
						color: 'bulue',
					},
					{
						name: 'twitter',
						title: '优先回答问题',
						color: '#dd6161',
					}
				]
			};
		},
		onShareAppMessage(res) {
			let uid = uni.getStorageSync('uid');
		    return {
		      title: '金牌律助',
		      path: '/pages/main/index/index?uid='+uid
		    }
		},
		onLoad() {
			_this = this;
			_this.getproduct();
			_this.getwarning();
		},
		methods: {
			getwarning(){
				uni.request({
					url:'/api/aichat/paypage',
					method:'GET',
					data:{},
					success(res) {
						_this.desc = res.data.data;
					}
				})
			},
			xuanzhong(index) {
				this.active=index
			},
			pay(){
				if(_this.active == -1){
					uni.showToast({
						icon:'error',
						title:'请选择卡型哦'
					})
					return false;
				}
				let token = uni.getStorageSync('tks');
				uni.request({
					url:'/api/aichat/getorder',
					method:'POST',
					data:{token:token, priductid:_this.active},
					success(res) {
						//拉起支付
						uni.requestPayment({
						    provider: 'wxpay',
							timeStamp: res.data.timeStamp,
							nonceStr: res.data.nonceStr,
							package: res.data.package,
							signType: res.data.signType,
							paySign: res.data.paySign,
							success: function (res) {
								uni.showToast({
									icon:'error',
									title:'操作成功',
									success() {
										uni.switchTab({
											url:'/pages/main/my/index'
										})
									}
									
								})
							},
							fail: function (err) {
								uni.showToast({
									icon:'error',
									title:'支付失败！'
								})
							}
						});
					}
				})
			},
			getproduct(){
				let token = uni.getStorageSync('tks');
				uni.request({
					url: '/api/aichat/product',
					data: {token:token},
					method: 'GET',
					success(res) {
						if(res.data.code == 1){
							res.data.data.forEach(function(item, index){
								_this.productList.push({
									'name':item.name,
									'id':item.id,
									'price':item.price,  //原价
									'now_price':item.now_price,  //现价
									'type':item.type,
								})
							})
						}
					},
					fail(err) {
						console.log(err);
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.share {
		position: fixed;
		right: 40rpx;
		margin-top: 20px;
		width: 40%;
	
		.u-button {
			box-shadow: 0rpx 10rpx 10rpx #eee !important;
		}
	}
	.activeClass1 {
		background-color: #26B3A0;
	}
	.activeClass2 {
		background-color: '';
	}
	.product{
		text-align: center;
		padding: 50rpx;
		border:1rpx solid navy;
		border-radius: 12rpx ;
		display:flex; 
		flex-direction: column;
		justify-content: stretch;
		line-height: 30px;
	}
	.form {
		width: 90%;
		margin: 0 auto;
		z-index: 999;

		.header {
			margin-bottom: 30rpx;

			.title {
				text-align: center;
				color: #fff;
				margin: 30rpx 0rpx;
			}
		}

		.panel {
			padding: 30rpx;
			background-color: #fff;
			border-radius: 15rpx;
			box-shadow: 0rpx 10rpx 10rpx #eee;
			margin-bottom: 15rpx;
			.bottom{
				font-size: 24rpx;
			}
			.head {
				border-bottom: 1rpx solid #eee;
				padding-bottom: 30rpx;
				.title{
					font-weight: bolder;
					font-size: 30rpx;
				}
			}
			.content{
				margin: 30rpx 0rpx;
			}
			.bottom{
				background-color: #F0FAF8;
				color: #26B3A0;
				padding: 15rpx;
				border-radius: 10rpx;
			}
		}
	}

	.bg {
		position: fixed;
		top: 0rpx;
		left: 0rpx;
		width: 100%;
		background-color: #26B3A0;
		min-height: 250rpx;
		border-radius: 0rpx 0rpx 100rpx 100rpx;
		z-index: -1;
	}
</style>
