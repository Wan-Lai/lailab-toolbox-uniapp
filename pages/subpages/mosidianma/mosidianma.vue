<template>
	<view class="container">
		<view class="top-block"></view>
		<textarea class="ms-input" placeholder="输入内容自动识别" maxlength="-1" @input="inputValue">
			<button class="ms-share" open-type="share"></button>
		</textarea>
		<text class="ms-output" @longtap="copyOutCode()">{{outcode?outcode:"长按复制"}}</text>
		
	</view>

</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				outcode: ''
			}
		},
		methods: {
			inputValue(e) {
				const that = this;
				// 获取到input的值
				const value = e.detail.value + ' .';
				var operate = 'encode';
				if (value.indexOf('-') >= 0) {
					operate = 'decode';
				}
				uni.request({
					url: app.globalData.website + '/tools/mosidianma.php?type=' + operate + '&text=' + value,
					method: 'POST',
					success(res) {
						// console.log(res);
						that.outcode = res.data;
					},
					fail(res) {
						console.log("请求失败", res);
						uni.showToast({
							content: '请求失败',
							icon: 'error'
						})
					}
				});
			},
			copyOutCode() {
				const that = this;
				if(that.outcode=='')return;
				//提示模板
				uni.showModal({
					title: "提示",
					content: that.outcode, //模板中提示的内容
					confirmText: '复制内容',
					success: (res) => { //点击复制内容的后调函数
						if (res.confirm) {
							//uni.setClipboardData方法就是讲内容复制到粘贴板
							uni.setClipboardData({
								data: that.outcode, //要被复制的内容
								success: () => { //复制成功的回调函数
									uni.showToast({ //提示
										title: '复制成功'
									})
								}
							});
						}
					}
				});
			},
		},
		onShareAppMessage() {
			return {
				title: '摩斯电码',
				imageUrl: app.globalData.website + '/image/swiper/image_swiper_mosidianma.jpg'
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

	.ms-input,
	.ms-output {
		width: 86%;
		height: 200px;
		border-radius: 20px;
		font-size: 20px;
		margin: 0 auto 20px auto;
		padding: 15px;
		background-color: #f6f7f9;
	}
	
	.ms-input {
		position: relative;
		font-weight: bold;
		color: #000000;
	}

	.ms-output {
		height: auto;
		max-height: 200px;
		display: block;
		color: #262626;
		margin: 0 auto 20px auto;
		overflow: scroll;
		white-space: pre-wrap;
		word-wrap: break-word;
	}
	
	.ms-share {
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
		background-color: rgba(84, 160, 255, .2);
		background-size: 60% 60%;
		z-index: 10;
	}
</style>
