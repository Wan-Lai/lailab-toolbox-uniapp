<template>
	<view class="container">
		<!-- 奖品图片 -->
		<view class="gift-show">
			<image class="gift-img" :src="picUrl" mode="aspectFill"></image>
			<button class="btn-switch" @tap="changeImage()">换</button>
		</view>
		<!-- 奖品名称 -->
		<view class="item gift-name">
			<text class="item-tip name-tip">奖品名称</text>
			<input class="item-input name" @input="bindAwardName" type="text" placeholder="请输入名称" />
		</view>
		<!-- 奖品数量 -->
		<view class="item gift-count">
			<text class="item-tip count-tip">奖品数量</text>
			<view class="item-input">
				<input class=" count" @input="bindAwardNum" type="number" maxlength="2" placeholder="数量" />
				<text class="item-tip count-unit">个</text>
			</view>
		</view>
		<!-- 奖品说明 -->
		<view class="item gift-info">
			<text class="item-tip info-tip">奖品说明</text>
			<input class="item-input" @input="bindAwardDetail" type="text" placeholder="请输入奖品说明" />
		</view>
		<!-- 开奖方式 -->
		<view class="item open-type">
			<text class="item-tip type-tip">开奖方式</text>
			<picker class="item-input type-picker" @change="bindOpentypeChange" :value="typeIndex" :range="openType">
				<view class="picker-text">
					请选择开奖方式
				</view>
			</picker>
			<text class="item-tip type-name">{{openType[typeIndex]}}</text>
		</view>
		<!-- 开奖设置 -->
		<view class="item open-setting" v-if="typeIndex!=2">
			<block v-if="typeIndex==0">
				<text class="item-etip info-tip">开奖时间</text>
				<view class="uni-list picker-date">
					<picker mode="date" :value="date" :start="startDate" :end="endDate" @change="bindDateChange">
						<view class="uni-input">{{date}}</view>
					</picker>
				</view>
				<view class="uni-list picker-time">
					<picker mode="time" :value="time" start="00:00" end="23:59" @change="bindTimeChange">
						<view class="uni-input">{{time}}</view>
					</picker>
				</view>
			</block>
			<block v-else-if="typeIndex==1">
				<!-- 通过人数开奖 -->
				<text class="item-tip count-tip">开奖人数</text>
				<view class="item-input">
					<input class=" count" @input="bindNeedUsers" type="number" maxlength="2" placeholder="数量" />
					<text class="item-tip count-unit">个</text>
				</view>
			</block>
		</view>
		<!-- 我的抽奖 -->
		<view class="gift-history">
			<!-- <text class="history-tip">我的抽奖</text> -->
			<view class="item item-history" v-for="(item,index) in giftList" :key="index">
				<text class="item-tip">{{item.name}}</text>
				<text class="item-tip">{{item.open?'已开奖':'待开奖'}}</text>
			</view>
		</view>
		<!-- 发起抽奖 -->
		<button class="gift-start" @tap="start()">发起抽奖</button>
		<!-- 分享抽奖 -->
		<button class="gift-share" v-if="isCreate" open-type="share">分享抽奖</button>
	</view>
</template>

<script>
	const app = getApp();

	export default {
		data() {
			const currentDate = this.getDate({
				format: true
			});
			const currenTime = this.getTime();
			return {
				picUrl: app.globalData.website + '/image/other/image_tool_choujiangzhushou.jpg',
				sharePicUrl: app.globalData.website + '/image/share/image_tool_choujiangzhushou.jpg',
				awardName: '',
				awardNum: 0,
				awardDetail: '',
				openType: ['按时间自动开奖', '按人数自动开奖', '发起者手动开奖'],
				typeIndex: 0,
				date: currentDate,
				time: currenTime,
				openNeedUsers: 0,
				awardId: '',
				isCreate: false,
				giftList: []
			}
		},
		computed: {
			startDate() {
				return this.getDate('start');
			},
			endDate() {
				return this.getDate('end');
			}
		},
		methods: {
			changeImage() {
				const that = this;
				wx.chooseImage({
					count: 1, //默认9
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album'], //从相册选择
					success(res) {
						that.picUrl = res.tempFilePaths[0];
					}
				})
			},
			// 获取奖品名称
			bindAwardName: function(e) {
				this.awardName = e.detail.value;
			},
			// 获取奖品数量
			bindAwardNum: function(e) {
				this.awardNum = e.detail.value;
			},
			// 获取奖品详情
			bindAwardDetail: function(e) {
				this.awardDetail = e.detail.value;
			},
			// 绑定抽奖方式
			bindOpentypeChange: function(e) {
				this.typeIndex = Number(e.detail.value);
			},
			// 绑定日期选择
			bindDateChange: function(e) {
				this.date = e.detail.value;
			},
			// 绑定时间选择
			bindTimeChange: function(e) {
				this.time = e.detail.value;
			},
			// 绑定所需用户数量
			bindNeedUsers: function(e) {
				this.openNeedUsers = Number(e.detail.value);
			},
			// 发起抽奖
			start() {
				const that = this;
				if(that.awardName=='') {
					that.toast('奖品名称','error');
					return;
				}
				if(that.awardNum == 0) {
					that.toast('奖品数量','error');
					return;
				}
				if(that.awardDetail == '') {
					that.toast('奖品说明','error');
					return;
				}
				if((that.typeIndex == 1)&&(that.openNeedUsers == 0)) {
					that.toast('抽奖人数','error');
					return;
				}
				if((that.typeIndex == 1)&&(that.awardNum >= that.openNeedUsers)) {
					that.toast('奖品太多','error');
					return; 
				}
				const openDateTime = this.date + ' ' + this.time;
				const startDateTime = this.getDate() + ' ' + this.getTime();
				let rst = {
					ownerId: app.globalData.openId,
					ownerImage: app.globalData.userInfo.avatarUrl,
					ownerNickName: app.globalData.userInfo.nickName,
					awardImage: this.picUrl,
					awardName: this.awardName,
					awardNum: this.awardNum,
					awardDetail: this.awardDetail,
					openType: this.typeIndex,
					startDateTime: startDateTime,
					openDateTime: openDateTime,
					openNeedUsers: this.openNeedUsers,
				};
				uni.showLoading({
					title: '抽奖创建中...',
					icon: 'loading'
				})
				uni.request({
					url: app.globalData.website + '/tools/choujiangzhushou/addAwardInfo.php',
					method: 'POST',
					data: rst,
					success(res) {
							console.log(res);
						if (res.data.err == 1) {
							uni.hideLoading();
							uni.showToast({
								title: '抽奖创建成功',
								icon: 'success'
							})
							that.awardId = res.data.result[0].id;
							if (that.awardId != '') {
								that.isCreate = true;
							}
						} else if (res.data.err == 0) {
							uni.hideLoading();
							uni.showToast({
								title: '抽奖创建失败',
								icon: 'error'
							})
						}
					},
					fail(res) {
						uni.showModal({
							title: '错误',
							content: res.errMsg
						})
					}
				})
			},
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();

				if (type === 'start') {
					year = year - 60;
				} else if (type === 'end') {
					year = year + 2;
				}
				month = month > 9 ? month : '0' + month;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			},
			getTime() {
				const date = new Date();
				let hour = date.getHours();
				let minute = date.getMinutes();
				hour = hour > 9 ? hour : '0' + hour;
				minute = minute > 9 ? minute : '0' + minute;
				return `${hour}:${minute}`;
			},
			toast(title, icon) {
				uni.showToast({
					title: title,
					icon: icon
				})
			}
		},
		onShareAppMessage() {
			if (this.isCreate) {
				return {
					title: '参与 ' + this.awardName + 'x' + this.awardNum + ' 抽奖',
					path: `/pages/subpages/choujiangzhushou/jiaruchoujiang?awardId=${this.awardId}`,
					imageUrl: (this.picUrl == (app.globalData.website + '/image/other/image_tool_choujiangzhushou.jpg') ?
						this.sharePicUrl : this.picUrl)
				}
			} else {
				uni.showToast({
					title: '创建失败',
					icon: 'error'
				})
			}
		}
	}
</script>

<style>
	.container {
		height: auto;
		width: 94vw;
		padding: 0 3vw 20px 3vw;
	}

	/* 奖品图片展示区域 */
	.gift-show {
		height: 200px;
		width: 100%;
		background-color: mediumpurple;
		border-radius: 20px;
		margin-bottom: 10px;
		position: relative;
	}

	/* 奖品图片 */
	.gift-img {
		width: 100%;
		height: 100%;
		border-radius: 20px;
		position: absolute;
	}

	/* 更换图片 */
	.btn-switch {
		height: 40px;
		width: 40px;
		border: 0;
		border-radius: 10px;
		line-height: 40px;
		text-align: center;
		background-color: #f6f7f9;
		color: black;
		position: absolute;
		top: 20px;
		right: 20px;
	}

	/* 奖品信息 */
	.item {
		height: 50px;
		width: 100%;
		background-color: #F6F7F9;
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 10px;
		border-radius: 15px;
	}

	/* 名称文字 */
	.item-tip {
		padding: 0 10px;
		flex: 1;
	}

	/* 输入框 */
	.item-input {
		display: flex;
		height: 100%;
		width: auto;
		flex: 4;
		padding: 0 10px;
		align-items: center;
	}

	/* 奖品数量输入框 */
	.count {
		width: 68%;
		text-align: right;
		padding-right: 10px;
		flex: 10;
		float: inline-start;
	}

	/* 奖品数量单位 */
	.count-unit {
		padding-left: 0;
		flex: 1;
	}

	/* 开奖方式文字 */
	.type-tip {
		flex: 1.6;
	}

	/* 开奖方式选择器 */
	.type-picker {
		flex: 3;
		/* background-color: red; */
	}

	/* 显示开奖方式 */
	.type-name {
		flex: 3;
		color: grey;
	}

	/* 日期选择框 */
	/* 时间选择器 */
	.picker-date,
	.picker-time {
		display: block;
		width: auto;
		justify-content: space-between;
		float:right;
		/* background-color: red; */
		margin-right: 10px;
	}
	
	.picker-date {
		flex: 7;
		text-align: right;
	}
	.picker-time {
		flex: 1;
	}
	
	/* 开奖时间 */
	.item-etip {
		padding: 10px;
		flex: 3;
	}

	/* 发起抽奖 */
	/* 分享抽奖 */
	.gift-start,
	.gift-share {
		height: 45px;
		margin-top: 20px;
		background-color: orangered;
		color: white;
		border: 0;
		border-radius: 20px;
		font-weight: bold;
		box-shadow: 0 0 10px orangered;
	}

	.gift-share {
		background-color: dodgerblue;
		box-shadow: 0 0 10px dodgerblue;
	}

	/* 抽奖历史 */
	.gift-history {
		max-height: 150px;
		overflow: scroll;
		background-color: #f6f7f9;
		border-radius: 20px;
		/* padding: 0 10px 0 10px; */
	}

	.history-tip {
		display: block;
		width: 100%;
		padding: 10px 0;
		border-bottom: 3px solid white;
	}

	.item-history {
		margin: 0;
		padding: 0;
		border-radius: 0;
		border-bottom: 3px solid white;
	}
</style>
