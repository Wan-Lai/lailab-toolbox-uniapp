<template>
	<view class="container">
		<view class="top-block"></view>
		<textarea class="tq-input" placeholder="请输入文字/网址" maxlength="-1" @input="getInputValue">
			<button class="tq-share" open-type="share"></button>
		</textarea>
		<image class="tq-qrcode" :src="url" show-menu-by-longpress="true"></image>
		<text class="tq-tip">长按图片进行保存</text>
	</view>

</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				url: ""
			}
		},
		methods: {
			getInputValue(e) {
				// 获取到input的值
				let value = e.detail.value;
				this.url = "https://www.lailab.cn/miniprogram/tools/qrcode.php?text=" + value;
			},
		},
		onShareAppMessage() {
			return {
				title: "二维码生成",
				imageUrl: app.globalData.website + '/image/swiper/image_swiper_erweima.jpg'
			};
		}
	}
</script>

<style>
	.container {
		height: 100vh;
		width: 100%;
		background-color: #54a0ff;
		margin: 0;
	}

	.top-block {
		height: 20px;
		width: 100%;
	}

	.tq-input {
		width: 86%;
		height: 200px;
		border-radius: 20px;
		font-size: 20px;
		margin: 0 auto 40px auto;
		padding: 15px;
		background-color: #f6f7f9;
		position: relative;
	}

	.tq-qrcode {
		height: 250px;
		width: 250px;
		background-color: white;
		margin: 70px auto;
		display: block;
	}

	.tq-tip {
		width: auto;
		color: white;
		margin-top: 50px;
		text-align: center;
		display: block;
	}

	.tq-share {
		position: absolute;
		top: 10px;
		right: 10px;
		width: 40px;
		height: 40px;
		border: none;
		border-radius: 10px;
		background-image: url('../../../static/icons/icon_share.png');
		background-repeat: no-repeat;
		background-position: center;
		background-color: rgba(84,160,255,.2);
		background-size: 60% 60%;
		z-index: 10;
	}
</style>
