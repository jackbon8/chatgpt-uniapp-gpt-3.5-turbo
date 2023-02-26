<template>
	<view class="container">
		
		<view class="answer">
			<view class="ask">{{title}}</view>
			<view class="content">
				<text>
					{{answer}}<strong></strong>
				</text>
			</view>
		</view>
		<view class="sheet">
			<view class="btn">
				<u-button icon="share" open-type="share" text="分享给好友"></u-button>
			</view>
			<view class="btn">
				<u-button  iconColor="#ffffff" @tap="copy(answerString)" color="#26B3A0" icon="file-text" text="复制问题答案"></u-button>
			</view>
		</view>
	</view>
</template>

<script>
	let _this = null;
	export default {
		data() {
			return {
				title:'请提出你的问题？',
				answer:' 🤔思考中....',
				answerString: '',
				
			};
		},
		onShareAppMessage(res) {
			let uid = uni.getStorageSync('uid');
		    return {
		      title: '金牌律助',
		      path: '/pages/main/index/index?uid='+uid
		    }
		},
		onLoad(option) {
			_this = this;
			_this.title = option.value;
			let from = option.from
			if(from != 'form'){
				_this.getinfo(option.value)
			} else {
				_this.gotoGPT(_this.title);
			}
		},
		methods:{
			getinfo(id){
				let token = uni.getStorageSync('tks');
				uni.request({
					url: '/api/aichat/answerinfo',
					data: {token:token, anid:id},
					method: 'POST',
					success(res) {
						if(res.data.code == 1){
							_this.title = res.data.data.title;
							_this.answer = res.data.data.answer;
							_this.answerString = res.data.data.answer;
						}
					},
					fail(err) {
						console.log(err);
					}
				})
			},
			async writing(){
				let answerArr = _this.answerString.split('');
				let timer = 150;
				_this.answer = ''; //清空默认值
				for(var i=0; i< answerArr.length; i++){
					_this.answer += answerArr[i];
					if(timer > 50) timer--;
					 await _this.sleep(timer);
				}
				
			},
			sleep(time){
			 return new Promise((resolve) => setTimeout(resolve, time));
			},
			gotoGPT(value){
				let header = {};
				let token = uni.getStorageSync('tks');
				uni.request({
					url: '/api/aichat/gpt',
					data: {content:value, token:token},
					method: 'POST',
					success(res) {
						if(res.statusCode == 200){
							if(res.data.code == 1){
								_this.answerString = res.data.data
								_this.writing();
								// _this.answer = res.data.data
								_this.addanswer(value, _this.answerString);
							} else {
								_this.$u.toast(res.data.msg);
									uni.switchTab({
										url:'/pages/main/form/index'
									})
							}
						}else{
							_this.$u.toast(res.errMsg);
							uni.switchTab({
								url:'/pages/main/form/index'
							})
						}
						
					},
					fail(err) {
						console.log(err);
					}
				})
			},
			copy(value){
				//uni.setClipboardData方法就是讲内容复制到粘贴板
				uni.setClipboardData({
					data:value,//要被复制的内容
					success:()=>{//复制成功的回调函数
						uni.showToast({//提示
						title:'复制成功'
						})
					}
				});
			},
			addanswer(title, answer){
				let token = uni.getStorageSync('tks');
				uni.request({
					url: '/api/aichat/addanswer',
					data: {title:title, answer:answer, token:token},
					method: 'POST',
					success(res) {
					},
					fail(err) {
						console.log(err);
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.answer {
		width: 90%;
		margin: 0 auto;
		margin-top: 55rpx;
		z-index: -1;

		.ask {
			font-size: 36rpx;
			font-weight: bolder;
		}

		.content {
			font-size: 30rpx;
			margin-top: 35rpx;
			color: #606266;
		}
	}

	.action {
		width: 92%;
		position: fixed;
		bottom: 0rpx;
		top: 0rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		height: 80rpx;
		align-items: center;
		box-shadow: 10rpx 10rpx 10rpx #eee;
		padding: 30rpx;
		z-index: 999;
		background-color: #fff;
	}

	.sheet {
		width: 100%;
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		bottom: env(safe-area-inset-bottom);
		padding: 30rpx 0rpx;
		background-color: #fff;
		box-shadow: -10rpx -10rpx 10rpx #eee;

		.btn {
			width: 42%;

			.u-button {
				height: 90rpx;
			}
		}
	}
</style>
