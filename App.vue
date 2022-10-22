<script>
	export default {
		globalData: {
			website: "https://www.lailab.cn/miniprogram",
			hasUserInfo: false,
			userInfo: {},
			openId: null
		},
		onLaunch: function() {
			this.login();
		},
		methods: {
			login() {
				const that = this
				if (this.globalData.hasUserInfo) {
					console.log("已获取用户信息");
				} else {
					//调用登录接口
					uni.login({
						success: function(res1) {
							// 获取个人 code
							uni.getUserInfo({
								success: res2 => {
									// 获取 OpenId
									wx.request({
										url: that.globalData.website +
											"/api/getOpenId.php",
										method: "POST",
										data: {
											code: res1.code,
										},
										header: {
											"Content-Type": "application/x-www-form-urlencoded"
										},
										success: function(res3) {
											if (res3.data.result.openid) {
												that.globalData.openId = res3.data
													.result.openid;
												console.log("获取OpenId成功");
												uni.request({
													url: that.globalData
														.website +
														"/api/getUserInfo.php",
													method: 'POST',
													data: {
														openId: res3.data
															.result.openid
													},
													success(res4) {
														if (res4.data.err ==
															0) {
															console.log(
																"用户未注册");
															uni.showModal({
																title: '提示',
																content: '检测到您尚未登录，某些工具可能无权使用，请先去登录。',
																confirmText: '去登陆',
																cancelText: '取消',
																success(res) {
																	if (res.confirm) {
																		that.userLogin();
																	}
																}
															})
														} else if (res4.data
															.err == 1) {
															console.log(
																"用户已注册");
															that.globalData
																.userInfo =
																res4.data
																.result[0];
															that.globalData
																.hasUserInfo =
																true;
														}
													}
												});
											} else {
												console.log("获取OpenId失败", res3);
											}
										},
										fail: function(res3) {
											console.log("访问OpenId服务器失败", res);
										}
									})
								},
								fail: function() {
									that.authorizeCheck("scope.userInfo");
								}
							})
						},
						fail(res) {
							console.log("调用登录服务失败", res);
						}
					})
				}
			},
			getRandomShareTitle: function() {
				var arr = [];

				if (this.globalData.shareapptext) {
					arr = this.globalData.shareapptext;
				}

				return arr[Math.floor(Math.random() * arr.length)];
			},
			getMainAppShare: function() {
				return {
					title: this.getRandomShareTitle(),
					path: '/pages/index/index',
					success: function(res) {
						// 转发成功
					},
					fail: function(res) {
						// 转发失败
					}
				}
			},
			userLogin() {
				const that = this;
				// 若用户未登录
				if (!that.globalData.hasUserInfo) {
					that.toast("正在登录", 'loading');
					uni.getUserProfile({
						desc: "获取你的昵称、头像、地区及性别",
						success: res => {
							that.globalData.hasUserInfo = true;
							that.globalData.userInfo = res.userInfo;
							that.toast("登录成功", 'success');
							uni.switchTab({
								url: '/pages/mine/mine'
							})
							const openId = that.globalData.openId;
							if (openId) {
								res.userInfo.openId = openId;
								uni.request({
									url: that.globalData.website + "/api/login.php",
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
					that.toast("您已经登录",'none');
				}
			},
			toast(title, icon) {
				uni.showToast({
					title: title,
					icon: icon
				})
			}
		}
	}
</script>

<style>
	/*每个页面公共css */
	uni-page-body,
	html,
	body {
		height: 100%;
	}
</style>
