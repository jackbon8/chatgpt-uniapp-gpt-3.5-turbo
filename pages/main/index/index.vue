<template>
	<view class="container">
		<view class="form">
			<view class="logo">
				<u--image src="/static/wxlogo.png" shape="circle" width="200rpx" height="200rpx">
				</u--image>
			</view>
			<u-transition :show="true" mode="slide-left">
				<view class="title">诸葛律助</view>
			</u-transition>
			<u-transition :show="true" mode="slide-right">
				<view class="desc">自研数据模型库，诸葛助你运筹帷幄！</view>
			</u-transition>
			<view class="btn-group">
				<view class="btn" >
					<u-button shape="circle" v-on:click="onToForm" iconColor="#ffffff" color="#26B3A0" icon="edit-pen" text="登录使用">
					</u-button>
					<!-- <u-button shape="circle" open-type="getPhoneNumber"  @getphonenumber="decryptPhoneNumber" iconColor="#ffffff" color="#26B3A0" icon="edit-pen" text="登录使用"></u-button>-->
				</view>
				<view class="btn">
					<u-button shape="circle" type="share" open-type="share" color="#26B3A0" :plain="true" icon="share" text="推荐给朋友"></u-button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	let _this = null;
	export default {
		data() {
			return {
				uid: '',
			};
		},
		onLoad(option) {
			_this = this;
			if(option.hasOwnProperty('uid')){
				_this.uid = option.uid;
			} 
		},
		methods: {
			decryptPhoneNumber (e) {
			   if(!e.detail.code){
				   uni.showToast({
					icon:'error',
				   	title:'登录失败!'
				   })
				   return false;
			   }
			   uni.login({
			     provider: 'weixin', //使用微信登录
			     success: function (loginRes) {
			   	uni.request({
			   		url:'/api/aichat/miniapplogin',
			   		data:{code:loginRes.code, uid:_this.uid, encopydata:e.detail.code},
			   		success(res) {
			   			if(res.data.code == 1){
			   				uni.setStorageSync('tks', res.data.data.token);
			   				uni.setStorageSync('uid', res.data.data.uid);
			   				uni.switchTab({
			   					url: '/pages/main/form/index'
			   				})
			   			} else{
			   				uni.showToast({
			   					title:res.data.msg
			   				})
			   			}
			   		}
			   	})
			     }
			   });
			   
			},
			onToForm() {
				uni.login({
				  provider: 'weixin', //使用微信登录
				  success: function (loginRes) {
					uni.request({
						url:'/api/aichat/miniapplogin',
						data:{code:loginRes.code, uid:_this.uid, encopydata:''},
						success(res) {
							if(res.data.code == 1){
								uni.setStorageSync('tks', res.data.data.token);
								uni.setStorageSync('uid', res.data.data.uid);
								uni.switchTab({
									url: '/pages/main/form/index'
								})
							} else{
								uni.showToast({
									title:res.data.msg
								})
							}
						}
					})
				  }
				});
			}
		}
	}
</script>

<style lang="scss">
	.form {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		margin-top: 50%;

		.title {
			font-size: 38rpx;
			font-weight: bolder;
			margin-top: 15rpx;
		}

		.desc {
			margin-top: 15rpx;
			font-size: 28rpx;
			color: #666;
		}

		.btn-group {
			width: 80%;

			.btn {
				margin: 30rpx 0rpx;

				.u-button {
					height: 100rpx;
				}
			}
		}
	}
</style>
