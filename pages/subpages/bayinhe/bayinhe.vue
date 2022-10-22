<template>
	<view class="byh-view">
		<image :src="picUrl" mode="widthFix" :animation="animationData"></image>
		<button class="byh-share" open-type="share"></button>
	</view>
	<view>
		<text @tap="audioPlay('jipigu')">鸡屁股</text>
		<text @tap="audioPlay('peidasuan')">配大蒜</text>
		<text @tap="audioPlay('jianjiandandan')">简简单单</text>
		<text @tap="audioPlay('yidunfan')">一顿饭</text>
	</view>
	<view>
		<text @tap="audioPlay('jishihui')">既实惠</text>
		<text @tap="audioPlay('haiguanbao')">还管饱</text>
		<text @tap="audioPlay('jiuchilaoba')">就吃老八</text>
		<text @tap="audioPlay('mizhihanbao')">蜜汁汉堡</text>
	</view>
	<view>
		<text @tap="audioPlay('yirisancan')">一日三餐</text>
		<text @tap="audioPlay('meifannao')">没烦恼</text>
	</view>
	<view>
		<text @tap="audioPlay('choudoufu')">臭豆腐</text>
		<text @tap="audioPlay('furu')">腐乳</text>
		<text @tap="audioPlay('jianingmeng')">加柠檬</text>
	</view>
	<view>
		<text @tap="audioPlay('nikanzhe')">你看这</text>
		<text @tap="audioPlay('hanbao')">汉堡</text>
		<text @tap="audioPlay('zuode')">做的</text>
		<text @tap="audioPlay('xingbuxing')">行不行</text>
	</view>
	<view>
		<text @tap="audioPlay('xiongdimen')">兄弟们</text>
		<text @tap="audioPlay('aoligei')">奥里给</text>
		<text @tap="audioPlay('zaojiuwanle')">造就完了</text>
	</view>
	<view>
		<text @tap="audioPlay('heiheihei')">嘿嘿嘿</text>
		<text @tap="audioPlay('panta')">盘它</text>
		<text @tap="audioPlay('lailea')">来了啊</text>
		<text @tap="audioPlay('meizizi')">美滋滋</text>
	</view>
	<view>
		<text @tap="audioPlay('ou1')">呕1</text>
		<text @tap="audioPlay('ou2')">呕2</text>
		<text @tap="audioPlay('ou3')">呕3</text>
		<text @tap="audioPlay('haihaihai')">嗨嗨嗨</text>
	</view>
	<view>
		<text @tap="audioPlay('huojimian')">火鸡面</text>
		<text @tap="audioPlay('dalajiao')">大辣椒</text>
		<text @tap="audioPlay('yidunbuchi')">一顿不吃</text>
		<text @tap="audioPlay('xincinao')">心刺挠</text>
	</view>
	<view>
		<text @tap="audioPlay('dsnhalg')">大声呐喊奥里给</text>
	</view>
	<view>
		<text @tap="audioPlay('sydxdxw')">所有东西都下胃</text>
	</view>
	<view>
		<text @tap="audioPlay('bgsxhsc')">不管是腥还是臭</text>
	</view>
	<view>
		<text @tap="audioPlay('dwzlskr')">到我嘴里是块肉</text>
	</view>
	<view>
		<text @tap="audioPlay('msjlwlb')">美食界里我老八</text>
	</view>
	<view>
		<text @tap="audioPlay('wrcwmsj')">万人称我美食家</text>
	</view>
	<view>
		<text @tap="audioPlay('lbmzxhb')">老八秘制小汉堡</text>
	</view>
	<view>
		<text @tap="audioPlay('lbzgh')">老八中国话</text>
		<text @tap="audioPlay('lbjl')">老八惊雷</text>
	</view>
	<view>
		<text @tap="audioPlay('szcshbz')">si在厕所汉堡中</text>
	</view>
	<view>
		<text class="btn-stop" @tap="audioPause" type="primary')">停止</text>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				picUrl: app.globalData.website + "/image/other/image_tool_bayinhe.jpg",
			}
		},
		methods: {
			audioPlay(flag) {
				let that = this;
				var srcMic = "";
				uni.request({
					url: "https://www.lailab.cn/miniprogram/tools/bayinhe.php?flag=" + flag,
					success(res) {
						// console.log(res);
						if (res.statusCode == 200) {
							srcMic = res.data;
							that.innerAudioContext = uni.createInnerAudioContext();
							//创建内部 audio 上下文 InnerAudioContext 对象。
							that.innerAudioContext.onError(function(res) {});
							if ((srcMic == '') || (srcMic == undefined)) return;
							that.innerAudioContext.src = srcMic; //设置音频地址
							that.innerAudioContext.play(); //播放音频
						} else {
							app.toast("网络错误", "error");
						}
					},
					fail() {
						app.toast("未知错误", "error");
					}
				})
			},
			// 停止播放
			audioPause() {
				if ((this.innerAudioContext == '') || (this.innerAudioContext == undefined)) return;
				this.innerAudioContext.pause(); //暂停音频 
			},
			toast(title, icon) {
				uni.showToast({
					title: title,
					icon: icon
				});
			}
		},
		onHide() {
			this.audioPause();
		},
		onUnload() {
			this.audioPause();
		}
	}
</script>

<style>
	view {
		justify-content: space-between;
		box-sizing: border-box;
		display: flex;
	}

	image {
		width: 96vw;
		height: auto;
		margin: 2vw;
		border-radius: 25px;
	}

	text {
		display: inline-block;
		text-align: center;
		padding: 10px;
		margin: 5px;
		border-radius: 10px;
		flex: 1;
		background-color: #54a0ff;
		color: white;
		font-weight: bold;
	}

	.btn-stop {
		width: 100%;
		background-color: #ff6b6b;
	}

	.byh-view {
		position: relative;
	}

	.byh-share {
		position: absolute;
		top: 20px;
		right: 20px;
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
