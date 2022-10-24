<template>
	<view class="container">
		<view class="user-info" open-type="getUserInfo" @tap="login()">
			<view class="info-img">
				<view class="img-bg"></view>
				<image class="img-icon" :src="hasUserInfo?userInfo.avatarUrl:''" mode="cover"></image>
			</view>
			<view class="info-text">
				<text class="text-name">{{hasUserInfo?userInfo.nickName:"点击登录"}}</text>
			</view>
		</view>
		<view class="items">
			<button class="item" open-type="contact">
				<image class="i-icon" src="/static/icons/icon_author_message.png" mode="widthFix"></image>
				<text class="i-name">联系作者</text>
				<text class="i-more iconfont icon-arrow-right"></text>
			</button>
			<button class="item feedback" @tap="feedback()">
				<image class="i-icon" src="/static/icons/icon_feedback.png" mode="widthFix"></image>
				<text class="i-name">反馈问题</text>
				<text class="i-more iconfont icon-arrow-right"></text>
			</button>
			<button class="item clearcatch" @tap="clearStorage()">
				<image class="i-icon" src="/static/icons/icon_clear_catch.png" mode="widthFix"></image>
				<text class="i-name">清理缓存</text>
				<text class="i-more iconfont icon-arrow-right"></text>
			</button>
			<button class="item devlog" @tap="log()">
				<image class="i-icon" src="/static/icons/icon_dev_log.png" mode="widthFix"></image>
				<text class="i-name">开发日志</text>
				<text class="i-more iconfont icon-arrow-right"></text>
			</button>
			<button class="item love" @tap="admire()">
				<image class="i-icon" src="/static/icons/icon_love.png" mode="widthFix"></image>
				<text class="i-name">捐赠作者</text>
				<text class="i-more iconfont icon-arrow-right"></text>
			</button>
			<button class="item share" open-type="share">
				<image class="i-icon" src="/static/icons/icon_share.png" mode="widthFix"></image>
				<text class="i-name">分享好友</text>
				<text class="i-more iconfont icon-arrow-right"></text>
			</button>
			<button class="item" open-type="openSetting">
				<image class="i-icon" src="/static/icons/icon_setting.png" mode="widthFix"></image>
				<text class="i-name">功能设置</text>
				<text class="i-more iconfont icon-arrow-right"></text>
			</button>
			<view v-if="hasUserInfo" class="item singout" @tap="logout()">
				<text class="i-name singout-text">退出登录</text>
			</view>
		</view>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				hasUserInfo: false,
				userInfo: {},
				openid: "",
			}
		},
		onLoad() {
			const that = this;
			if (app.globalData.hasUserInfo) {
				this.hasUserInfo = app.globalData.hasUserInfo;
				this.userInfo = app.globalData.userInfo;
			}
		},
		methods: {
			login() {
				const that = this;
				// 若用户未登录
				if (!app.globalData.hasUserInfo) {
					that.toast("正在登录", 'loading');
					uni.getUserProfile({
						desc: "获取你的昵称、头像、地区及性别",
						success: res => {
							app.globalData.hasUserInfo = true;
							app.globalData.userInfo = res.userInfo;
							that.hasUserInfo = true;
							that.userInfo = res.userInfo;
							that.toast("登录成功", 'success');
							const openId = app.globalData.openId;
							if (openId) {
								res.userInfo.openId = openId;
								// console.log("res",res);
								uni.request({
									url: app.globalData.website + "/api/login.php",
									method: 'POST',
									data: res.userInfo,
									success(res) {
										uni.showModal({
											title: "提示",
											content: res.msg
										})
									}
								})
							} else {
								console.log("没有OpenId,请重新进入小程序");
							}

						},
						fail: res => {
							that.toast("您拒绝了请求", 'error');
						}
					})
				} else {
					uni.showToast({
						title: "您已经登录",
						icon: 'none'
					})
				}
			},
			logout() {
				const that = this;
				uni.showModal({
					title: "提示",
					content: "是否确定退出登录？",
					complete(res) {
						if (res.confirm) {
							app.globalData.hasUserInfo = false;
							app.globalData.userInfo = {};
							that.hasUserInfo = false;
							that.userInfo = {};
						} else if (res.cancel) {
							that.toast("取消操作", 'success');
						}
					}
				})
			},
			feedback() {
				const mail = "lailab_mail@126.com";
				const qq = "1694319867";
				uni.showModal({
					title: "提示",
					content: "有问题可以通过联系客服进行反馈,也可以添加作者QQ[1694319867]或发送邮箱[lailab_mail@126.com]进行反馈,点击下面按钮可以进行一键复制。",
					confirmText: "邮箱",
					cancelText: "QQ",
					confirmColor: "#000",
					success(res) {
						var msg = "";
						if (res.confirm) {
							msg = mail;
						} else if (res.cancel) {
							msg = qq;
						}
						uni.setClipboardData({
							data: msg,
							success(res) {
								uni.getClipboardData({
									success(res) {
										uni.showToast({
											title: '复制成功',
											icon: "success",
										});
									}
								})
							}
						})
					}
				});
			},
			share() {},
			// 获取本地缓存大小
			getStorageSize() {
				let that = this;
				try {
					plus.cache.calculate(function(size) {
						let sizeCache = parseInt(size);
						if (sizeCache == 0) {
							that.currentSize = "0B";
						} else if (sizeCache < 1024) {
							that.currentSize = sizeCache + "B";
						} else if (sizeCache < 1048576) {
							that.currentSize = (sizeCache / 1024).toFixed(2) + "KB";
						} else if (sizeCache < 1073741824) {
							that.currentSize = (sizeCache / 1048576).toFixed(2) + "MB";
						} else {
							that.currentSize = (sizeCache / 1073741824).toFixed(2) + "GB";
						}
					});
				} catch (e) {
					return;
				}
			},
			// 清理缓存
			clearStorage() {
				let that = this;
				that.toast("正在清理", "loading");
				try {
					let os = plus.os.name;

					if (os == 'Android') {
						let main = plus.android.runtimeMainActivity();
						let sdRoot = main.getCacheDir();
						let files = plus.android.invoke(sdRoot, "listFiles");
						let len = files.length;
						console.log(files, len)
						for (let i = 0; i < len; i++) {
							let filePath = '' + files[i]; // 没有找到合适的方法获取路径，这样写可以转成文件路径  
							plus.io.resolveLocalFileSystemURL(filePath, function(entry) {
								if (entry.isDirectory) {
									entry.removeRecursively(function(entry) { //递归删除其下的所有文件及子目录
										that.files = []
										that.toast("清除成功", "success");
										that.getStorageSize(); // 重新计算缓存  
									}, function(e) {
										console.log(e.message)
									});
								} else {
									entry.remove();
								}
							}, function(e) {
								console.log('文件路径读取失败')
							});
						}
					} else { // ios  
						plus.cache.clear(function() {
							that.toast("清除成功", "success");
							that.getStorageSize();
						});
					}
				} catch {
					that.toast("清理完成", "success");
					return;
				}
			},
			log() {
				const log = `
					<b>v1.0 2022-10-7</b><br />
						1.从微信端进行移植<br /><br />
					<b>v2.0 2022-10-19</b><br />
						1.更新主页空内容显示<br />
						2.实现工具智能抠图、证件照、摩斯电码、随机骰子等功能<br />
						3.实现部分登录逻辑<br /><br />
					<b>v2.1 2022-10-22</b><br />
						1.添加自动登录逻辑<br />
					<b>v2.1 2022-10-22</b><br />
						1.添加自动登录逻辑<br />
						2.各个界面微调美化<br />
					<b>v2.2 2022-10-23</b><br />
						1.添加抽奖助手界面<br />
					<b>v2.2.1 2022-10-24</b><br />
						1.完善抽奖助手子页面<br />
				`;
				uni.navigateTo({
					url: `/pages/utils/showText/showText?nodes=${log}`
				});
			},
			admire() {
				const url = app.globalData.website + "/image/other/image_admire.jpg"
				uni.navigateTo({
					url: `/pages/utils/showImg/showImg?id=${0}&url=${url}&showBar=false&showMode=aspectFit&canLongTap=true`
				});
			},
			toast(title, icon) {
				uni.showToast({
					title: title,
					icon: icon
				})
			},
		},
		onLoad() {
			if (app.globalData.hasUserInfo) {
				this.hasUserInfo = app.globalData.hasUserInfo;
				this.userInfo = app.globalData.userInfo;
			}
			this.getStorageSize();
		},
		onShow() {
			if (app.globalData.hasUserInfo) {
				this.hasUserInfo = app.globalData.hasUserInfo;
				this.userInfo = app.globalData.userInfo;
			}
		},
		onShareAppMessage() {
			return {
				title: 'LaiLab工具箱',
				path: 'pages/mine/mine'
			}
		}

	}
</script>

<style>
	@import url("../../static/iconfont/iconfont.css");

	.container {
		width: 100%;
		/* background-color: red; */
		background-color: #ffffff;
		padding: 20px 0 80px 0;
		/* text-align: center; */
	}

	.user-info {
		width: 90%;
		height: 140px;
		background-color: #f6f7f9;
		border-radius: 15px;
		margin: 0 auto;
	}

	.info-img {
		width: 30%;
		height: 100%;
		/* background-color: green; */
		float: left;
		text-align: center;
		position: relative;
	}

	.img-bg {
		height: 80px;
		width: 80px;
		border-radius: 15px;
		background-color: #eeeeee;
		position: absolute;
		top: 30px;
		left: 20px;
	}

	.img-icon {
		height: 60px;
		width: 60px;
		/* background-color: red; */
		border-radius: 10px;
		position: absolute;
		top: 40px;
		left: 30px;
	}

	.info-text {
		width: 70%;
		height: 100%;
		/* background-color: skyblue; */
		float: right;
	}

	.text-name {
		font-size: 30px;
		line-height: 140px;
		font-weight: bold;
	}

	button::after {
		border: none;
	}

	.items {
		width: 90%;
		background-color: #ffffff;
		border-radius: 15px;
		margin: 20px auto;
		padding: 0;
	}

	.item {
		height: 60px;
		line-height: 60px;
		text-align: left;
		background-color: #f6f7f9;
		border-radius: 0;
	}

	.item:first-child {
		border-radius: 20px 20px 0 0;
	}

	.item:last-child {
		border-radius: 0 0 20px 20px;
	}

	.i-icon {
		height: 30px;
		width: 30px;
		/* background-color: red; */
		float: left;
		margin: 15px;
	}

	.i-name {
		font-size: 18px;
		padding-left: 5px;
	}

	.i-more {
		display: block;
		height: 30px;
		width: 40px;
		line-height: 40px;
		margin: 10px auto;
		color: #888888;
		font-size: 17px;
		float: right;
	}

	/* 退出登录字体 */
	.singout-text {
		width: 100%;
		display: inline-block;
		color: #ff6b6b;
		font-weight: bold;
		text-align: center;
	}
</style>
