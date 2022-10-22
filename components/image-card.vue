<template>
	<!-- 主页展示卡片 -->
	<view class="ic-container">
		<image class="ic-image" mode="widthFix" :src="url" lazy-load="true"/>
		<text class="ic-source" :class="'level-'+Math.ceil(Math.random()*9)">{{id}}</text>
	</view>
</template>

<script>
	export default {
		name: "image-card",
		props: {
			url: {
				type: String,
				value: ""
			},
			id: {
				type: String,
				value: ""
			},
			source: {
				type: String,
				value: ""
			}
		},
		data() {
			return {
				isShow: false
			};
		},
		methods: {
			showImage() {
				this.isShow = true;
			},
			closeImage() {
				this.isShow = false;
			}
		}
	}
</script>

<style>
	.ic-container {
		position: relative;
	}

	.ic-image {
		min-height: 200px;
		width: 100%;
		border-radius: 20px;
		margin-top: 10px;
		background-color: #f6f7f9;
	}


	.ic-source {
		min-height: 15px;
		min-width: 15px;
		line-height: 15px;
		padding: 3px 7px;
		border-radius: 7px;
		font-size: 12px;
		position: absolute;
		top: 20px;
		left: 15px;
		opacity: 0.5;
	}

	.level-1 {
		background-color: #c8d6e5;
	}

	.level-2 {
		background-color: #576574;
		color: white;
	}

	.level-3 {
		background-color: #222f3e;
		color: white;
	}

	.level-4 {
		background-color: #48dbfb;
	}

	.level-5 {
		background-color: #54a0ff;
	}

	.level-6 {
		background-color: #5f27cd;
		color: white;
	}

	.level-7 {
		background-color: #feca57;
	}

	.level-8 {
		background-color: #ff9ff3;
	}

	.level-9 {
		background-color: #ff6b6b;
		clear: white;
	}
</style>
