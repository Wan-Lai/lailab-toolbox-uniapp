<template>
	<view class="container">
		<!-- 奖品展示 -->
		<view class="gift-show">
			<image class="gift-img" :src="picUrl"></image>
			<text class="gift-name">奖品:精美老八x8</text>
			<text class="gift-open">10月23日 11:00 自动开奖</text>
		</view>
		<!-- 操作视图 -->
		<view class="action-view">
			<button class="gift-add">参与抽奖</button>
			<text class="gift-person">已有100人参与</text>
			<view class="handimgs">
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
				<image class="handimg" :src="picUrl" mode="aspectFill"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				picUrl: 'https://www.lailab.cn/miniprogram/image/other/image_tool_bayinhe.jpg'
			}
		},
		methods: {

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
		background-color: #f6f7f9;
	}

	/* 奖品图片展示 */
	.gift-img {
		width: 100%;
		height: 400px;
	}

	/* 奖品名称 */
	.gift-name {
		display: block;
		font-size: 23px;
		line-height: 40px;
		padding-left: 10px;
	}

	/* 奖品开奖信息 */
	.gift-open {
		display: block;
		font-size: 18px;
		line-height: 25px;
		padding-left: 10px;
		color: gray;
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
		margin-top: 20px;
		background-color: orangered;
		color: white;
		border: 0;
		border-radius: 20px;
		font-weight: bold;
		box-shadow: 0 0 10px orangered;
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
		/* background-color: red; */
		text-align: center;
		overflow: scroll;
	}

	/* 各个图像 */
	.handimg {
		height: 50px;
		width: 50px;
		margin: 2px;
		border-radius: 50%;
		background-color: green;
	}
</style>
