<template>
	<view class="container">
		<view class="bg-muyu">
			<switch class="sw-animation" @change="changeAnimation()">{{useanim?'关':'开'}}动画</switch>
			<text class="te-sumgongde">今日已累计 {{num*gongde}} 功德</text>
		</view>
		<image class="img-muyu" :src="bgurl" mode="widthFix"></image>
		<button class="dzmy-share" open-type="share"></button>
		<view class="v-muyu" @tap="audioPlay">
			<view class="v-gongde" :animation="animationData">功德+{{gongde}}</view>
		</view>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				bgurl: "https://www.lailab.cn/miniprogram/image/other/image_tool_muyu.jpg",
				num: 0,
				gongde: 500,
				useanim: false,
				animationData: {}
			}
		},
		methods: {
			audioPlay: function(e) {
				let that = this;
				var srcMic = "https://www.lailab.cn/owncloud/index.php/s/RMVFmfrDy0He7Ka/download";
				const x = e.detail.x;
				const y = e.detail.y;
				if (this.useanim) {
					this.startAnimation(x, y);
				}
				// if (that.innerAudioContext == undefined) {
				// console.log("请求了");
				that.innerAudioContext = uni.createInnerAudioContext();
				//创建内部 audio 上下文 InnerAudioContext 对象。
				that.innerAudioContext.onError(function(res) {});
				if ((srcMic == '') || (srcMic == undefined)) return;
				that.innerAudioContext.src = srcMic; //设置音频地址
				// }
				that.audioPause();
				that.innerAudioContext.play(); //播放音频
				that.num += 1;
			},
			// 停止播放
			audioPause() {
				if ((this.innerAudioContext == '') || (this.innerAudioContext == undefined)) return;
				this.innerAudioContext.pause(); //暂停音频 
			},
			startAnimation(x, y) {
				this.animation.translate(x - 110, y - 400).opacity(1).step();
				this.animationData = this.animation.export();
				setTimeout(function() {
					this.animation.opacity(0).translate(x - 110, y - 600).step();
					this.animationData = this.animation.export()
				}.bind(this), 300)
				setTimeout(function() {
					this.animation.translate(x - 110, y - 380).opacity(0).step();
					this.animationData = this.animation.export()
				}.bind(this), 600);
			},
			// 开关动画
			changeAnimation() {
				console.log("转化", this.useanim);
				this.useanim = !this.useanim;
			},
		},
		onShow: function() {
			var animation = uni.createAnimation({
				duration: 300,
				timingFunction: 'ease',
			})
			this.animation = animation
			this.animationData = animation.export()
		},
		onShareAppMessage() {
			return {
				title: '敲木鱼',
				imageUrl: app.globalData.website + '/image/swiper/image_swiper_qiaomuyu.jpg'
			};
		}
	}
</script>

<style>
	.containr {
		height: 1000px;
		width: 100%;
		position: relative;
		background-color: black;
	}

	.sw-animation {
		font-size: 18px;
		line-height: 45px;
		/* background-color: red; */
		padding: 10px;
		color: white;
	}

	.te-sumgongde {
		line-height: 45px;
		padding: 10px;
		/* background-color: green; */
		float: right;
		font-size: 18px;
		color: white;
	}

	.bg-muyu {
		height: 100px;
		width: 100%;
		background-color: black;
		position: absolute;
		top: 0;
		left: 0;
	}

	.img-muyu {
		width: 100%;
		position: absolute;
		bottom: 0;
	}

	.dzmy-share {
		position: absolute;
		top: 60px;
		right: 15px;
		width: 40px;
		height: 40px;
		border: none;
		border-radius: 10px;
		background-image: url('../../../static/icons/icon_share.png');
		background-repeat: no-repeat;
		background-position: center;
		background-color: #84602D;
		background-size: 60% 60%;
		z-index: 10;
		float: right;
	}

	.v-muyu {
		width: 65vw;
		height: 220px;
		position: absolute;
		bottom: 210px;
		left: 15vw;
	}

	.v-gongde {
		font-size: 20px;
		color: white;
		opacity: 0;
	}
</style>
