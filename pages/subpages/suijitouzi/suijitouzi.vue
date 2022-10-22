<template>
	<view class="container">
		<button class="sjtz-share" open-type="share"></button>
		<view class="row row-1">
			<text v-if="count>0" class="dice"
				:style="{backgroundPosition: position[nums[0]].posX+'px '+position[nums[0]].posY+'px'}"></text>
			<text v-if="count>5" class="dice"
				:style="{backgroundPosition: position[nums[5]].posX+'px '+position[nums[5]].posY+'px'}"></text>
			<text v-if="count>1" class="dice"
				:style="{backgroundPosition: position[nums[1]].posX+'px '+position[nums[1]].posY+'px'}"></text>
		</view>
		<view v-if="count>2" class="row row-2">
			<text v-if="count>2" class="dice"
				:style="{backgroundPosition: position[nums[2]].posX+'px '+position[nums[2]].posY+'px'}"></text>
			<text v-if="count>4" class="dice"
				:style="{backgroundPosition: position[nums[4]].posX+'px '+position[nums[4]].posY+'px'}"></text>
			<text v-if="count>3" class="dice"
				:style="{backgroundPosition: position[nums[3]].posX+'px '+position[nums[3]].posY+'px'}"></text>
		</view>
		<picker class="picker" @change="countChange" :value="index" :range="array">
			<view class="picker-text">
				当前选择：{{array[index]}} v
			</view>
		</picker>

		<button class="btn-start" @tap="start()">{{sum}}</button>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				position: [{
						posX: 0,
						posY: 0,
					},
					{
						posX: -100,
						posY: 0,
					},
					{
						posX: -200,
						posY: 0,
					},
					{
						posX: -300,
						posY: 0,
					},
					{
						posX: -400,
						posY: 0,
					},
					{
						posX: -500,
						posY: 0,
					}
				],
				count: 1,
				nums: [0, 1, 2, 3, 4, 5],
				array: ['1个骰子', '2个骰子', '3个骰子', '4个骰子', '5个骰子', '6个骰子'],
				index: 0,
				sum: '开始',
			}
		},
		methods: {
			start() {
				clearInterval(this.timer);
				this.sum = "等待计算中";
				this.timer = setInterval(this.picFn, 50);
				setTimeout(() => {
					clearInterval(this.timer);
					let num = 0;
					for (var i = 0; i < this.nums.length; i++) {
						if (i >= this.count) {
							this.nums[i] = 0;
						}
					}
					this.nums.forEach(item => {
						num += item
					});
					this.sum = `分数：${num+this.count}`;
				}, 2000);
			},
			randNum: () => Math.floor(Math.random() * 6),
			picFn() {
				this.nums = this.nums.map(item => this.randNum())
			},
			countChange: function(e) {
				// console.log('picker发送选择改变，携带值为', e.detail.value);
				let num = e.detail.value;
				this.index = num;
				this.count = ++num;
			},
		}
	}
</script>

<style>
	.container {
		height: 100vh;
		width: 100vw;
		background-color: #f6f7f9;
		justify-content: center;
		box-sizing: border-box;
		position: relative;
	}

	.row {
		width: 90vw;
		justify-content: space-between;
		box-sizing: border-box;
		display: flex;
		margin: 0 5vw;
	}

	.row-1 {
		padding-top: 150px;
	}

	.row-2 {
		margin-top: 20px;
	}

	.dice {
		display: block;
		height: 100px;
		width: 100px;
		background-color: red;
		background: url('https://www.lailab.cn/miniprogram/image/other/image_tool_suijitouzi.png') no-repeat;
		margin: auto;
	}

	.picker,
	.btn-start {
		height: 45px;
		width: 90vw;
		margin: 0 5vw;
		text-align: center;
		line-height: 45px;
		font-size: 20px;
		margin-top: 30px;
		border: none;
		border-radius: 10px;
	}

	.picker {
		background-color: #262626;
		color: white;
	}

	.btn-start {
		background-color: #262626;
		color: white;
	}
	
	.sjtz-share {
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
		background-color: rgba(38, 38, 38, .5);
		background-size: 60% 60%;
		z-index: 10;
	}
</style>
