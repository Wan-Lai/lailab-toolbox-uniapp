<template>
	<view class="container">
		<!-- 奖品展示 -->
		<view class="gift-show">
			<image class="gift-img" :src="picUrl" mode="aspectFill"></image>
			<text class="gift-name">奖品: {{awardName}} x {{awardNum}}份</text>
			<text
				class="gift-open">{{openType==0?openDateTime + ' ':(openType==1?'满' + openNeedUsers + '人':'')}}自动开奖</text>
			<text v-if="awardDetail!=''" class="gift-detail-tip">奖品详情</text>
			<text v-if="awardDetail!=''" class='gift-detail'>{{awardDetail}}</text>
		</view>
		<!-- 操作视图 -->
		<view class="action-view">
			<button :class="isOpened?'gift-add-disable':'gift-add'" @tap="isJoin?load():join()" :disabled="!isClick">{{isJoin?(isOpened?(isLockyClover?'您已中奖':'您未中奖'):'等待开奖'):'参与抽奖'}}</button>
			<text class="gift-person">已有 {{joinCount}} 人参与</text>
			<view class="handimgs">
				<image class="handimg" mode="aspectFill" v-for="(item,index) in joinList" :key="index"
					:src="item.userImg"></image>
			</view>
		</view>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				awardId: 0,
				picUrl: 'https://www.lailab.cn/miniprogram/image/share/image_tool_choujiangzhushou.jpg',
				awardName: '',
				awardNum: 0,
				awardDetail: '',
				openType: 0,
				openDateTime: '2000-01-01 00:00',
				userId: 0,
				joinList: [],
				joinCount: 0,
				isOpened: false,
				isJoin: false,
				isClick: false,
				isLockyClover: false
			}
		},
		onLoad(option) {
			const that = this;
			that.awardId = option.awardId;
			that.refresh();
		},
		methods: {
			join() {
				const that = this;
				uni.showLoading({
					title: '正在加入',
					icon: 'loading'
				});
				uni.request({
					url: app.globalData
						.website +
						"/tools/choujiangzhushou/userJoin.php",
					method: 'POST',
					data: {
						awardId: that.awardId,
						userId: that.userId
					},
					success(res1) {
						console.log('5.用户加入', res1);
						uni.hideLoading();
						if (res1.data.err == 0) {
							uni.showToast({
								title: '加入失败',
								icon: 'error',
							});
						} else if (res1.data.err == 1) {
							uni.showToast({
								title: '加入成功',
								icon: 'success'
							})
							that.isJoin = true;
						} else if (res1.data.err == 2) {
							that.isClick = false;
							uni.showLoading({
								title: '正在开奖中',
								icon: 'loading'
							});
							// 是否中奖
							that.checkLuckyClover();
						} else if (res1.data.err == 3) {
							that.isClick = false;
							uni.showToast({
								title: "开奖失败",
								icon: 'error'
							})
						} else {
							uni.showModal({
								title: '提示',
								content: res1.data.msg
							})
						}
						that.refresh();
					}
				});
			},
			load() {
				var msg = '条件';
				if (this.openType == 0) {
					msg += this.openDateTime + ' ';
				} else if (this.openType == 1) {
					msg += '满' + this.openNeedUsers + '人';
				}
				msg += '开奖未满足,请等待开奖!';
				uni.showModal({
					title: '等待开奖',
					content: msg,
				})
			},
			refresh() {
				const that = this;
				that.joinList = [];
				that.isJoin = false;
				that.isClick = false;
				// 请求奖品信息
				uni.request({
					url: app.globalData.website + '/tools/choujiangzhushou/getAwardInfo.php',
					method: 'POST',
					data: {
						awardId: that.awardId
					},
					success(res) {
						console.log("1.通过奖品ID获取奖品信息", res);
						if (res.data.result.length > 0) {
							const rst = res.data.result[0];
							that.picUrl = rst.awardImage;
							that.awardName = rst.awardName;
							that.awardNum = rst.awardNum;
							that.awardDetail = rst.awardDetail;
							that.openDateTime = rst.openDateTime;
							that.openType = rst.openType;
							that.openNeedUsers = rst.openNeedUsers;
							that.isOpened = rst.isOpened==1;
						} else {
							uni.showToast({
								title: '加载失败',
								icon: 'error'
							})
						}
						if (that.isOpened) {
							that.isClick = false;
						}
					}
				});
				uni.request({
					url: app.globalData.website + "/api/getUserInfo.php",
					method: 'POST',
					data: {
						openId: app.globalData.openId
					},
					success(res) {
						console.log('2.通过openId获取用户Id', res);
						if (res.data.result) {
							that.userId = res.data.result[0].id;
						} else {
							uni.hideLoading();
							console.log(res);
							uni.showToast({
								title: '加载成功',
								icon: 'success'
							})
						}
						// 若开奖,查看是否中奖品，放这里因为这里产生userId
						if(that.isOpened) {
							// 检测是否中奖
							that.checkLuckyClover();
						}
					}
				});
				// 通过奖品ID查询各种信息
				uni.request({
					url: app.globalData.website + '/tools/choujiangzhushou/userJoinInfo.php',
					method: 'POST',
					data: {
						awardId: that.awardId
					},
					success(res) {
						console.log('3.通过奖品Id获取参与用户ID', res);
						const users = res.data.result;
						that.joinCount = Number(users.length);
						for (var i = 0; i < users.length; i++) {
							const user = users[i];
							uni.request({
								url: app.globalData.website + '/api/getUserInfoById.php',
								method: 'POST',
								data: {
									id: user.userId
								},
								success(res1) {
									console.log('4.通过userId获取用户信息(图像)', res1);
									for (var i = 0; i < res1.data.result.length; i++) {
										that.joinList.push({
											userImg: res1.data.result[0].avatarUrl
										});
									}
								}
							});
							if (user.userId == that.userId) {
								that.isJoin = true;
							}
						}
						setTimeout(function() {
							if(!that.isOpened){
								that.isClick = true;
							}
						}, 1000);
					},
					fail(res) {
						console.log('访问失败', res);
					}
				});
				// 若开奖停用按钮
				if (that.isLockyClover) {
					that.isClick = false;
				}
			},
			checkLuckyClover() {
				const that = this;
				uni.request({
					url: app.globalData.website + '/tools/choujiangzhushou/isluckyClover.php',
					method: 'POST',
					data: {
						awardId: that.awardId,
						userId: that.userId
					},
					success(res) {
						console.log('6.查看是否中奖', res);
						uni.hideLoading();
						if (res.data.result) {
							that.isLockyClover = res.data.result[0].luckyClover == 1;
						}
					}
				})
			}
		},
		onPullDownRefresh() {
			this.refresh();
			setTimeout(function() {
				uni.hideNavigationBarLoading();
				//完成停止加载图标
				uni.stopPullDownRefresh();
			}, 1000);
		},
		onShareAppMessage() {
			return {
				title: '参与抽奖'
			}
		}
	}
</script>

<style>
	.container {
		height: auto;
		width: 100vw;
		/* padding: 0 3vw 20px 3vw; */
	}

	/* 奖品展示区域 */
	.gift-show {
		height: auto;
		width: 100%;
		background-color: #ffffff;
		padding: 10px 0;
	}

	/* 奖品图片展示 */
	.gift-img {
		width: 100%;
		height: 350px;
	}

	/* 奖品名称 */
	.gift-name {
		display: block;
		font-size: 18px;
		line-height: 30px;
		padding-left: 20px;
		color: #262626;
	}

	/* 奖品开奖信息 */
	.gift-open {
		display: block;
		font-size: 12px;
		line-height: 15px;
		padding-left: 20px;
		margin: 5px 0 10px 0;
		color: gray;
	}

	/* 奖品详情文字 */
	.gift-detail-tip {
		display: inline-block;
		width: 100%;
		/* background-color: green; */
		border-top: 10px solid #f6f7f9;
		font-size: 15px;
		color: #262626;
		line-height: 30px;
		padding-left: 20px;
	}

	/* 奖品详细信息 */
	.gift-detail {
		width: 100%;
		display: inline-block;
		padding: 2px 20px 20px 20px;
		font-size: 17px;
		color: #262626;
		border-top: 2px solid #f6f7f9;
		border-bottom: 10px solid #f6f7f9;
	}

	/* 操作视图 */
	.action-view {
		height: auto;
		width: 94vw;
		padding: 0 3vw 20px 3vw;
	}

	/* 参与抽奖 */
	.gift-add {
		height: 45px;
		margin-top: 10px;
		background-color: orangered;
		color: white;
		border: 0;
		border-radius: 20px;
		font-weight: bold;
		box-shadow: 0 0 10px orangered;
	}
	
	/* 参与抽奖-不启用 */
	.gift-add-disable {
		height: 45px;
		margin-top: 10px;
		color: white;
		border: 0;
		border-radius: 20px;
		font-weight: bold;
		box-shadow: 0;
	}
	
	/* 参与人数 */
	.gift-person {
		line-height: 30px;
		font-size: 15px;
	}

	/* 图像视图 */
	.handimgs {
		height: auto;
		width: 100%;
		text-align: center;
		overflow: scroll;
	}

	/* 各个图像 */
	.handimg {
		height: 50px;
		width: 50px;
		margin: 2px;
		border-radius: 50%;
		background-color: #F6F7F9;
	}
</style>
