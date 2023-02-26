<template>
	<view class="container" style="height: 100%;">
		<view class="bg"></view>
		<view style="margin-left: 30px; display: flex;" >
			<view>
				<u-avatar :src="src" size="60"></u-avatar>
			</view>
			<view style="display: flex; flex-direction: column; padding-left: 20rpx; height: 60px;">
				<view style="padding: 7px  0px;">{{nickname}}</view>
				<view style="padding: 5px  0px;">
					<text v-if="vip == 1">🔥剩余查询次数:{{score}} 次</text>
					<text v-if="vip == 2">🔥VIP到期时间:{{end_vip_time}}</text>
					<text style="margin-left: 20rpx;">💰coin:{{money}}</text>
				</view>
			</view>
		</view>
		
		<view class="tipsfather">
			<view class="tips">
				<view v-if="vip == 1">当前身份: 普通用户</view>
				<view v-if="vip == 2">当前身份: 👑 <text style="color: #ff9900;">VIP用户</text></view>
				<view style="margin-top: 6rpx;">
					<text>
						{{zhengce}}
					</text>
				</view>
				
			</view>
			
		</view>
		<view class="note">
			<view class="noteson">
				<u-notice-bar :text="note" ></u-notice-bar>
			</view>
		</view>
			
		<view class="form" style="padding-top: 40rpx;">
			<u-grid :border="false" @click="click" >
				<u-grid-item v-for="(baseListItem,baseListIndex) in baseList" :key="baseListIndex" >
					<u-icon
							:customStyle="{paddingTop:20+'rpx'}"
							:name="baseListItem.name"
							:size="42"
							:color="baseListItem.color" 
					></u-icon>
					<text class="grid-text">{{baseListItem.title}}</text>
				</u-grid-item>
			</u-grid>
			<u-toast ref="uToast" />
		</view>
	</view>
</template>

<script>
	let _this = null;
	export default {
		data() {
			return {
				end_vip_time:'',
				nickname:'',
				score:0,
				money:'0.00',
				vip:1,
				src: '/static/image/549.jpg',
				note: 'uView UI众多组件覆盖开发过程的各个需求，组件功能丰富，多端兼容。让您快速集成，开箱即用',
				zhengce: '充值流量,立即解锁智能聊天机器人提问\n加入推广员赚佣金,佣金高达20%',
				baseList: [
					{
						name: 'integral-fill',
						title: '会员充值',
						color: 'gold',
						value: '/pages/main/chongzhi/index'
					},
					{
						name: 'chat-fill',
						title: '问答记录',
						color: '#26B3A0',
						value: '/pages/main/ask/index'
					},
					{
						name: 'server-man',
						title: '客服信息',
						color: 'bulue',
						value: '/pages/main/ask/index'
					}
				]
			};
		},
		onLoad() {
			_this = this;
		},
		onShow() {
			_this.userinfo();
		},
		methods: {
			userinfo(){
				let token = uni.getStorageSync('tks');
				uni.request({
					url:'/api/aichat/userinfos',
					method:'POST',
					data:{token:token},
					success(res) {
						_this.zhengce = res.data.data.note1;
						_this.note = res.data.data.note2;
						_this.vip = res.data.data.level;
						_this.nickname = res.data.data.nickname;
						_this.score = res.data.data.score;
						_this.money = res.data.data.money;
						_this.end_vip_time = res.data.data.end_vip_time;
					}
				})
			},
			click(name) {
				if(name == 0){
					//充值
					uni.navigateTo({
						url:_this.baseList[name].value
					})
				} else if(name == 1){
					uni.navigateTo({
						url:_this.baseList[name].value
					})
				}else{
					uni.setClipboardData({
						data:'jsonp9',
						success() {
							_this.$refs.uToast.success(`已复制客服微信: jsonp9`)
						}
					})
				}
				
			}
		}
	}
</script>

<style lang="scss">
	 .grid-text {
	        font-size: 14px;
	        color: #909399;
	        padding: 10rpx 0 20rpx 0rpx;
	        /* #ifndef APP-PLUS */
	        box-sizing: border-box;
	        /* #endif */
	    }
	.form {
		width: 90%;
		margin: 10rpx auto;
		z-index: 999;
		background-color: white;
		border-radius: 10rpx;
		height: 840rpx;
	}
	.note{
		width: 100%;
		margin: 0 auto;
		display:flex;
		justify-content:center;
		.noteson{
			margin-top: 10px;
			border-radius: 8rpx;
			width: 90%;
		}
	}
	.tipsfather{
		width: 100%;
		margin: 0 auto;
		display:flex;
		justify-content:center;
		.tips{
			padding: 25rpx 50rpx;
			margin-top: 10px;
			background-color: #F0FAF8;
			border-radius: 8rpx;
			width: 90%;
		}
	}
	.bg {
		position: fixed;
		top: 0rpx;
		left: 0rpx;
		width: 100%;
		background-color: #26B3A0;
		min-height: 200rpx;
		border-radius: 0rpx 0rpx 40rpx 40rpx;
		z-index: -1;
	}
</style>
