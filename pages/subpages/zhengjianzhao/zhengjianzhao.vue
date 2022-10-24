<template>
	<view class="container">
		<view class="tmp"></view>
		<view class="view-show" @tap="chooseImg()">
			<image class="img-show" :src="imgUrl" :style="{'backgroundColor':color}" mode="heightFix" @tap="showImg()"
				@load="loadImg()" @error="errImg()" show-menu-by-longpress></image>
			<button class='zjz-share' open-type="share" @tap.stop="testFn($event)"></button>
		</view>
		<view class="colors">
			<text class="colors-tip">选择背景色</text>
			<view class="colors-choose">
				<text class="color color-red" @tap="changeColor('#D9001B')">红</text>
				<text class="color color-blue" @tap="changeColor('#02A7F0')">蓝</text>
				<text class="color color-white" @tap="changeColor('#FFFFFF')">白</text>
				<text class="color color-black" @tap="changeColor('#222f3e')">黑</text>
				<text class="color color-more" @tap="changeColor('#ff9ff3')">粉</text>
			</view>
		</view>
		<button class="upload" @tap="startMake()">开始制作</button>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				isChoose: false,
				imgUrl: '',
				color: '#00000000',
			}
		},
		methods: {
			chooseImg() {
				const that = this;
				console.log(!this.isChoose);
				if (!this.isChoose) {
					wx.chooseImage({
						count: 1, //默认9
						sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
						sourceType: ['album'], //从相册选择
						success(res) {
							that.imgUrl = res.tempFilePaths[0];
							that.isChoose = true;
						}
					})
				}
			},
			showImg() {
				if (this.isChoose) {
					uni.navigateTo({
						url: `/pages/utils/showImg/showImg?id=${0}&url=${this.imgUrl}&source=${this.imgUrl}`
					});
				}
			},
			changeColor(col) {
				this.color = col;
			},
			startMake() {
				const that = this;
				uni.showLoading({
					title: '正在生成',
					icon: 'loading'
				});
				uni.uploadFile({
					url: app.globalData.website + "/tools/zhengjianzhao.php",
					filePath: that.imgUrl,
					name: 'pic',
					formData: {
						color: that.color,
					},
					success(res) {
						console.log(res, '上传成功');
						if (res.data.length > 5) {
							uni.hideLoading();
							uni.showToast({
								title: '生成成功',
								icon: 'success'
							})
							that.imgUrl = res.data;
							uni.showLoading({
								title: '图片加载中...',
								icon: 'loading'
							})
						} else {
							uni.hideLoading();
							uni.showToast({
								title: '生成失败',
								icon: 'error'
							})
						}
					},
					fail(err) {
						uni.hideLoading();
						uni.showToast({
							title: '生成失败',
							icon: 'error'
						})
					}
				})
			},
			loadImg() {
				uni.hideLoading();
				uni.showToast({
					title: '图片加载成功',
					icon: 'success',
				})
			},
			errImg() {
				uni.hideLoading();
				uni.showToast({
					title: '图片加载失败',
					icon: 'error',
				})
			},
			testFn(e) {
				e.preventDefault();
			}
		},
		onShareAppMessage() {
			return {
				title: '证件照助手',
				imageUrl: app.globalData.website + '/image/swiper/image_swiper_zhengjianzhao.jpg'
			}
		}
	}
</script>

<style>
	.container {
		height: 100vh;
		width: 100vw;
	}

	.tmp {
		height: 3vh;
	}

	.view-show {
		height: 65vh;
		width: 90vw;
		background: url("https://www.lailab.cn/miniprogram/image/other/image_zhengjianzhao_bg.jpg");
		background-size: 100% 100%;
		background-position: center;
		background-repeat: no-repeat;
		margin: 0 5vw;
		border-radius: 25px;
		text-align: center;
		overflow-x: scroll;
		overflow-y: hidden;
		/*解决ios上滑动不流畅*/
		-webkit-overflow-scrolling: touch;
		position: relative;
	}

	.zjz-share {
		position: fixed;
		top: 40px;
		right: 40px;
		width: 40px;
		height: 40px;
		border: none;
		border-radius: 10px;
		background-image: url('../../../static/icons/icon_share.png');
		background-repeat: no-repeat;
		background-position: center;
		background-color: rgba(34, 47, 62, .1);
		background-size: 60% 60%;
		z-index: 10;
	}


	.img-show {
		height: 100%;
		min-width: auto;
		/* border-radius: 25px; */
		background-color: transparent;
	}

	.colors {
		height: 15vh;
		width: 90vw;
		margin: 2vh 5vw 0 5vw;
		background-color: #eee;
		border-radius: 20px;
	}

	.colors-tip {
		height: 30%;
		width: 100%;
		font-size: 18px;
		display: flex;
		justify-content: center;
		align-items: center;
		text-align: center;
		color: #333;
	}

	.colors-choose {
		height: 70%;
		width: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.color {
		height: 50px;
		width: 50px;
		display: inline-block;
		background-color: #eee;
		line-height: 50px;
		text-align: center;
		margin: 10px;
		border-radius: 25px;
		color: white;
	}

	.color-red {
		background-color: #D9001B;
	}

	.color-blue {
		background-color: #02A7F0;
	}

	.color-white {
		background-color: #ffffff;
		color: #333;
	}

	.color-black {
		background-color: #222f3e;
	}

	.color-more {
		background-color: #ff9ff3;
	}

	.upload {
		height: 45px;
		width: 90vw;
		margin: 2vh 5vw 0 5vw;
		border-radius: 20px;
		border: 0;
		background-color: #222f3e;
		color: white;
	}
</style>
