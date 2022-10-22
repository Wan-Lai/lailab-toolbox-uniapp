<template>
	<view class="tc-container" @tap="turn2Url(url)">
		<view class="tc-text">
			<text class="tc-title">{{title}}</text>
			<text class="tc-detail">{{detail}}</text>
		</view>
		<view class="tc-img">
			<text class="tc-bg" :style="{'backgroundColor':color}"></text>
			<image class="tc-icon" :src="icon" mode="scaleToFill"></image>
		</view>
	</view>
</template>

<script>
	export default {
		name: "tool-card",
		props: {
			title: {
				type: String,
				value: ""
			},
			detail: {
				type: String,
				value: ""
			},
			icon: {
				type: String,
				value: ""
			},
			color: {
				type: String,
				value: ""
			},
			url: {
				type: String,
				value: ""
			}
		},
		data() {
			return {

			};
		},
		methods: {
			turn2Url(iurl) {
				uni.navigateTo({
					url: iurl,
					success() {
						uni.showToast({
							title: "正在加载",
							icon: "loading",
							duration: 500
						});
					},
					fail() {
						uni.showToast({
							title: "开发中...",
							icon: "error"
						});
					}
				})
			}
		}
	}
</script>

<style>
	.tc-container {
		width: 100%;
		height: 90px;
		background-color: #f8f8f8;
		border-radius: 20px;
	}

	.tc-text {
		width: 70%;
		float: left;
	}

	.tc-title {
		margin: 15px 15px 2px 15px;
		height: 20px;
		line-height: 20px;
		display: inline-block;
		font-size: 18px;
		overflow: hidden;
	}

	.tc-detail {
		margin: 0 0 0 15px;
		height: 15px;
		line-height: 15px;
		display: inline-block;
		font-size: 12px;
		color: #888888;
		overflow: hidden;
	}

	.tc-img {
		width: 30%;
		float: left;
		text-align: center;
		position: relative;
	}

	.tc-bg {
		display: block;
		height: 36px;
		width: 36px;
		margin: 12px;
		border-radius: 18px;
		background-color: pink;
		position: absolute;
		right: 0;
		top: 0;
	}

	.tc-icon {
		height: 24px;
		width: 24px;
		margin: 12px;
		border-radius: 18px;
		position: absolute;
		right: 6px;
		top: 6px;
	}
</style>
