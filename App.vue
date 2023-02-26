<script>
	export default {
		onLaunch: function() {
			const updateManager = uni.getUpdateManager();
			updateManager.onCheckForUpdate(function (res) {
			  // 请求完新版本信息的回调
			  console.log('onLaunch checkAppUpdate',res.hasUpdate);
			});
			
			updateManager.onUpdateReady(function (res) {
			  uni.showModal({
			    title: '更新提示',
			    content: '新版本已经准备好，是否重启应用？',
			    success(res) {
			      if (res.confirm) {
			        // 新的版本已经下载好，调用 applyUpdate 应用新版本并重启
			        updateManager.applyUpdate();
			      }
			    }
			  });
			
			});
			
			updateManager.onUpdateFailed(function (res) {
			  // 新的版本下载失败
			});
			
			//未登录直接跳转到登录页面
			let token = uni.getStorageSync('tks');
			if(!token){
				uni.navigateTo({
					url:'/pages/main/index/index'
				})
			}else{
				//登录过期重新登录
				uni.request({
					url:'/api/aichat/checktoken',
					data:{token:token},
					success(res) {
						if(res.data.code != 1){
							uni.setStorageSync('tks', '');
							uni.navigateTo({
								url:'/pages/main/index/index'
							})
						}
					}
				})
			}
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	/* 注意要写在第一行，同时给style标签加入lang="scss"属性 */
	@import "uview-ui/index.scss";
	@import "colorui/main.css";
	@import "colorui/icon.css";
	@import "static/css/index-app.css";
	page{
		font-size: 28rpx;
	}
</style>