<template>
	<view class="container">
		<view class="bg"></view>
		<view class="form">
			<view class="header">
				<view class="title">人工智能回答你需要的问题</view>
			</view>
			
			<view v-for="(item, index) in answerlist" :key="index" class="panel" @tap="onSubmit(item.id)">
				<view class="head">
					<view class="title">问：{{item.title}}{{item.id}}</view>
				</view>
				<view class="content">
					{{item.answer}}
				</view>
				<view class="bottom">
					回答时间：{{item.createtime}}
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
				answerlist: []
			};
		},
		onLoad() {
			_this = this;
			this.getanswerList();
		},
		methods: {
			onSubmit(id) {
				uni.navigateTo({
					url: '/pages/main/answer/index?from=ask&value='+id
				})
			},
			getanswerList(){
				let token = uni.getStorageSync('tks');
				uni.request({
					url: '/api/aichat/answerlist',
					data: {token:token},
					method: 'POST',
					success(res) {
						if(res.data.code == 1){
							console.log(res.data.data);
							res.data.data.forEach(function(item, index){
								_this.answerlist.push({
									'title' :  item.title,
									'answer' :  item.answer,
									'createtime' :  item.createtime,
									'id' : item.id
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
		min-height: 200rpx;
		border-radius: 0rpx 0rpx 140rpx 140rpx;
		z-index: -1;
	}
</style>
