<template>
	<view class="container">
		<!-- 主体区域 -->
		<view class="main">
			<!-- 提示区域 -->
			<view class="tip" :class="'level-'+tip.level" v-if="(tip!='')&&(tip.isShow)" @tap="showTip()">
				<text class="tip-text">
					{{tip.msg}}
				</text>
			</view>
			<view class="tip" v-else></view>
			<!-- 轮播图 -->
			<swiper class="swiper" v-if="(swiper!='')" :indicator-dots="swiper.indicatorDots"
				:vertical="swiper.vertical" :circular="swiper.circular" :interval="swiper.interval"
				:duration="swiper.duration" :autoplay="swiper.autoplay">
				<swiper-item v-for="(item,index) in swiper.background" :key="index"
					@tap="item.clickable?swiperTap(item.action,item.content):null">
					<view class="swiper-item" v-if="item.type=='color'" :style="{backgroundColor:item.background}">
						{{item.text}}
					</view>
					<image class="swiper-item" v-if="item.type=='image'" :src="item.background" :mode="item.imageMode">
					</image>
				</swiper-item>
			</swiper>
			<view class="swiper" v-else></view>
			<!-- 图片展示区域 -->
			<view class="pics" v-if="(pics!='')">
				<!-- 瀑布流左边 -->
				<view class="left">
					<icard class="icard" v-for="(item,index) in leftList" :key="index" :url="item.url"
						:source="item.source" :id="item.id" @tap="showImg(item)"></icard>
				</view>
				<!-- 瀑布流右边 -->
				<view class="right">
					<icard class="icard" v-for="(item,index) in rightList" :key="index" :url="item.url"
						:source="item.source" :id="item.id" @tap="showImg(item)"></icard>
				</view>
			</view>
			<view class="pics" v-else>
				<!-- 瀑布流左边 -->
				<view class="left">
					<icard class="icard-left-tmp"></icard>
					<icard class="icard-left-tmp"></icard>
				</view>
				<!-- 瀑布流右边 -->
				<view class="right">
					<icard class="icard-right-tmp"></icard>
					<icard class="icard-right-tmp"></icard>
				</view>
			</view>
			<view class="pic-show-more" @tap="showMorePics()">加载更多</view>
		</view>
	</view>
</template>

<script>
	import icard from "@/components/image-card.vue";
	export default {
		components: {
			icard,
		},
		data() {
			return {
				"picPage": 1,
				"picSize": 4,
				// 初始化左右盒子
				"leftList": [],
				"rightList": [],
				// 初始化左右盒子高度
				"leftH": 0,
				"rightH": 0,
				'tip': '',
				'swiper': '',
				'pics': ''
			}
		},
		onLoad() {
			this.getDataByOnline();
		},
		onPullDownRefresh() {
			const that = this;
			this.leftList = [];
			this.rightList = [];
			this.tip = {};
			this.swiper = {};
			this.pics = '';
			setTimeout(function() {
				that.getDataByOnline();
				uni.showToast({
					title: "刷新成功",
					duration: 500
				})
				uni.hideNavigationBarLoading();
				//完成停止加载图标
				uni.stopPullDownRefresh();
			}, 1000);
		},
		methods: {
			getDataByOnline() {
				const that = this;
				uni.request({
					url: 'https://www.lailab.cn/miniprogram/data.php?type=all',
					success(rst) {
						that.tip = rst.data.tip;
						that.swiper = rst.data.swiper;
						that.showPics();
					},
				});
			},
			doList() {
				const that = this;
				// console.log("pics", this.pics);
				if ((this.pics == '') || (this.pics == undefined)) return;
				that.pics.forEach(res => {
					// 获取图片宽高
					uni.getImageInfo({
						src: res.url,
						success: (image) => {
							// console.log("image", image);
							// 计算图片渲染高度
							let showH = (50 * image.height) / image.width
							// 判断左右盒子高度
							if (that.leftH <= that.rightH) {
								that.leftList.push(res);
								that.leftH += showH;
							} else {
								that.rightList.push(res);
								that.rightH += showH;
							}
						}
					})
				})
			},
			showTip() {
				uni.showModal({
					title: "提示",
					content: this.tip.msg,
					confirmText: "我知道了",
					showCancel: false
				})
			},
			showMorePics() {
				uni.showToast({
					title: "加载更多",
					icon: "loading"
				});
				this.picPage += 1;
				this.showPics();
			},
			showPics() {
				const that = this;
				uni.request({
					url: "https://www.lailab.cn/miniprogram/pics.php?page=" + that.picPage + "&size=" + that
						.picSize,
					success(rst) {
						// console.log(rst);
						that.pics = rst.data;
						that.doList();
					}
				})
			},
			showImg(pic) {
				uni.navigateTo({
					url: `/pages/utils/showImg/showImg?id=${pic.id}&url=${pic.url}&source=${pic.source}`
				})
			},
			swiperTap(action, content) {
				switch (action) {
					case "dialog":
						uni.showModal({
							title: "提示",
							content: content
						})
						break;
					case "goto":
						uni.navigateTo({
							url: content
						})
						break;
				}
			}
		}
	}
</script>

<style>
	.container {
		height: 100%;
		width: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	/* 主体区域 */
	.main {
		height: 100%;
		width: 100%;
	}

	/* 提示区域 */
	.tip {
		width: auto;
		height: 30px;
		margin: 10px 10px 0 10px;
		overflow: hidden;
		border-radius: 15px;
		padding: 5px;
		background-color: #f6f7f9;
		justify-content: center;
	}

	/* 提示区域文字 */
	.tip-text {
		width: auto;
		white-space: nowrap;
		animation: 10s loop linear infinite normal;
		display: inline-block;
		color: white;
		vertical-align: top;
		font-size: 15px;
		line-height: 30px;
		font-weight: bold;
	}

	/* swiper轮播图样式 */
	.swiper {
		height: 200px;
		width: auto;
		margin: 10px 10px 5px 10px;
		border-radius: 20px;
		background-color: #f6f7f9;
	}

	.swiper-item {
		height: 100%;
		width: 100%;
		text-align: center;
		color: white;
		font-size: 40px;
		line-height: 200px;
		border-radius: 20px;
	}

	/* 展示图片 */
	.pics {
		padding: 0 30rpx;
		font-size: 14rpx;
		line-height: 24rpx;
	}

	.right,
	.left {
		display: inline-block;
		width: 48%;
		vertical-align: top;
	}

	.left {
		margin-right: 4%;
	}

	.icard-left-tmp {
		height: 200px;
		background-color: green;
	}

	.icard-right-tmp {
		height: 150px;
		background-color: green;
	}

	/* 图片下方加载更多 */
	.pic-show-more {
		display: block;
		height: 40px;
		margin: 10px;
		border-radius: 20px;
		text-align: center;
		line-height: 40px;
		background-color: #ff6b6b;
		font-weight: bold;
		font-size: 18px;
		color: white;
	}

	/* 各种等级文字及背景颜色 */
	.level-1 {
		background-color: #222f3e;
		color: white;
	}

	.level-2 {
		background-color: #54a0ff;
		color: white;
	}

	.level-3 {
		background-color: #feca57;
		color: white;
	}

	.level-4 {
		background-color: #ff6b6b;
		color: white;
	}

	/* 提示框平移动画 */
	@keyframes loop {
		0% {
			transform: translateX(350px);
			-webkit-transform: translateX(350px);
		}

		100% {
			transform: translateX(-100%);
			-webkit-transform: translateX(-100%);
		}
	}
</style>
