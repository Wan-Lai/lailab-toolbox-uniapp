<template>
	<view class="container">
		<!-- 奖品图片 -->
		<view class="gift-show">
			<image class="gift-img" :src="picUrl" mode="aspectFill"></image>
			<button class="btn-switch">换</button>
		</view>
		<!-- 奖品名称 -->
		<view class="item gift-name">
			<text class="item-tip name-tip">奖品名称</text>
			<input class="item-input name" type="text" placeholder="请输入名称" />
		</view>
		<!-- 奖品数量 -->
		<view class="item gift-count">
			<text class="item-tip count-tip">奖品数量</text>
			<view class="item-input">
				<input class=" count" type="number" maxlength="2" placeholder="数量" />
				<text class="item-tip count-unit">个</text>
			</view>
		</view>
		<!-- 奖品说明 -->
		<view class="item gift-info">
			<text class="item-tip info-tip">奖品说明</text>
			<input class="item-input" type="text" placeholder="请输入奖品说明" />
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
				<text class="item-tip info-tip">开奖时间</text>
				<view class="uni-list picker-date">
					<picker mode="date" :value="date" :start="startDate" :end="endDate" @change="bindDataChange">
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
					<input class=" count" type="number" maxlength="2" placeholder="数量" />
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
		<button class="gift-start">发起抽奖</button>
		<!-- 分享抽奖 -->
		<button class="gift-share" v-if="isCreate" open-type="share">分享抽奖</button>
	</view>
</template>

<script>
	export default {
		data() {
			const currentDate = this.getDate({
				format: true
			})
			const currenTime = this.getTime();
			return {
				picUrl: 'https://www.lailab.cn/miniprogram/image/other/image_tool_bayinhe.jpg',
				openType: ['按时间自动开奖', '按人数自动开奖', '发起者手动开奖'],
				typeIndex: 0,
				date: currentDate,
				time: currenTime,
				isCreate: true,
				giftList: [
					{
						name: '我的抽奖1',
						open: true
					},
					{
						name: '我的抽奖2',
						open: false
					},
					{
						name: '我的抽奖3',
						open: true
					},
					{
						name: '我的抽奖4',
						open: false
					},
					{
						name: '我的抽奖5',
						open: true
					},
					{
						name: '我的抽奖6',
						open: false
					},
				]
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
			bindOpentypeChange: function(e) {
				this.typeIndex = e.detail.value;
			},
			bindDataChange: function(e) {
				this.date = e.detail.value;
			},
			bindTimeChange: function(e) {
				this.time = e.detail.value;
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
			}
		},
		onShareAppMessage() {
			return {
				title: '参与抽奖',
				path: '/pages/subpages/choujiangzhushou/jiaruchoujiang',
				imageUrl: this.picUrl
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
		border: 0;
		border-radius: 20px;
		height: 50px;
		width: 50px;
		background-color: pink;
		color: white;
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
		padding-right: 0;
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
		margin-right: 10px;
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
		border-bottom:3px solid white ;
	}
	.item-history {
		margin: 0;
		padding: 0;
		border-radius: 0;
		border-bottom: 3px solid white;
	}
</style>
