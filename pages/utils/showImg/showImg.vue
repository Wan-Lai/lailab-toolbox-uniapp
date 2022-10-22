<template>
	<view class="container">
		<view class="view-show">
			<image class="img-show" :src="picUrl" @load="loadImg()" @error="errImg()" :mode="showMode" :show-menu-by-longpress="canLongTap"></image>
		</view>
		<button class="btn btn-back" @tap="back()">X</button>
		<view v-if="showBar&&hindBar" class="btnbar">
			<button class="btn btn-copyright" @tap="copyright()">版权</button>
			<button class="btn btn-share" open-type="share">分享</button>
			<button class="btn btn-download" @tap="download()">下载</button>
			<button class="btn btn-source" @tap="sourceImg()">原图</button>
			<button class="btn btn-hide" @tap="hideBar()">隐藏</button>
			<!-- 版权 分享 下载 原图 -->
		</view>
		<button v-if="!hindBar" class="btn btn-show" @tap="showBarFn()">开</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				id: "",
				url: "",
				source: "",
				picUrl: "",
				showMode: "heightFix",
				hindBar: true,
				showBar: true,
				canLongTap: false
			}
		},
		onLoad(option) {
			console.log('获取数据成功');
			this.id = option.id?option.id:this.id;
			this.url = option.url?option.url:this.url;
			this.source = option.source?option.source:this.source;
			this.showMode = option.showMode?option.showMode:this.showMode;
			this.showBar = option.showBar?(option.showBar.indexOf('t')==0?true:false):this.showBar;
			this.canLongTap = option.canLongTap?(option.canLongTap.indexOf('t')==0?true:false):this.canLongTap;
			this.picUrl = this.url;
			uni.showLoading({
				title: '图片加载中...',
				icon: 'loading'
			})
		},
		methods: {
			back() {
				try{
					uni.navigateBack();
				}catch(e){
					uni.switchTab({
						url: "/pages/home/home"
					})
				}
			},
			hideBar() {
				this.hindBar = false;
			},
			showBarFn() {
				this.hindBar = true;
			},
			copyright() {
				uni.showModal({
					title: '提示',
					content: '图片皆为网上搜寻，若侵权。请联系我进行删除。',
					success: function(res) {
						if (res.confirm) {
							console.log('确定');

						} else if (res.cancel) {
							console.log('取消');
						}
					}
				});
			},
			share() {
				uni.showModal({
					title: "提示",
					content: "长按图片进行分享操作!"
				});
			},
			download() {
				const link = this.picUrl;
				// 展示进度框
				uni.showLoading({
					title: "开始下载",
					icon: "loading",

				})
				uni.downloadFile({
					url: link,
					success(res) {
						if (res.statusCode == 200) {
							uni.getFileSystemManager().saveFile({
								tempFilePath: res.tempFilePath,
								filePath: `${wx.env.USER_DATA_PATH}/image.png`,
								success(res) {
									console.log(res.savedFilePath);
									wx.saveImageToPhotosAlbum({
										filePath: res.savedFilePath,
										success(res) {
											console.log(res)
											console.log('保存图片成功')
											uni.hideLoading();
											wx.showToast({
												title: '下载成功',
											})
										}
									})
								},
								fail() {
									uni.hideLoading();
									uni.showModal({
										title: "提示",
										content: "下载失败,可以通过长按图片进行保存。"
									});
								}
							});
						}
					},
					fail() {
						uni.hideLoading();
						uni.showModal({
							title: "提示",
							content: "下载失败,可以通过长按图片进行保存。"
						});
					}
				});
			},
			sourceImg() {
				this.picUrl = this.source;
				uni.showLoading({
					title: '图片加载中...',
					icon: 'loading'
				})
			},
			loadImg() {
				uni.hideLoading();
				uni.showToast({
					title: '图片加载成功',
					icon: 'success'
				});
			},
			errImg() {
				uni.hideLoading();
				uni.showToast({
					title: '图片加载失败',
					icon: 'error'
				});
			}
		}
	}
</script>

<style>
	.container {
		height: 100vh;
		width: 100vw;
		position: relative;
	}

	.view-show {
		height: 100%;
		width: 100%;
		position: absolute;
		top: 0;
		left: 0;
		overflow-x: scroll;
		overflow-y: hidden;
		/*解决ios上滑动不流畅*/
		-webkit-overflow-scrolling: touch;
		text-align: center;
	}

	.img-show {
		height: 100%;
		min-width: 100vw;
		width: auto;
	}

	.btn {
		line-height: 60px;
		text-align: center;
		background-color: transparent;
		color: white;
		border: 0;
		display: flex;
		flex: 1;
	}

	.btn-back {
		height: 40px;
		width: 40px;
		line-height: 40px;
		color: white;
		background-color: rgba(0, 0, 0, 0.6);
		border-radius: 20px;
		text-align: center;
		position: absolute;
		top: 50px;
		left: 10px;
	}

	.btnbar {
		height: 60px;
		width: 80vw;
		background-color: rgba(0, 0, 0, 0.6);
		border-radius: 20px;
		display: flex;
		position: absolute;
		bottom: 5vh;
		left: 10vw;
	}

	.btn-show {
		height: 40px;
		width: 40px;
		line-height: 40px;
		color: white;
		background-color: rgba(0, 0, 0, 0.6);
		border-radius: 20px;
		text-align: center;
		position: absolute;
		bottom: 6vh;
		right: 10px;
	}
</style>
