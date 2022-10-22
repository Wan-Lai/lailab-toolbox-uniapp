<template>
	<view class="container">
		<view class="main">
			<view class="classify-cards" v-for="(items,index) in tools" :key="index">
				<text class="classify-name" style="margin: 10px auto;">{{items.name}}</text>
				<view>
					<tcard class="tool-card" v-for="(item,index1) in items.cards" :key="index1" :title="item.title" :detail="item.detail"
						:color="item.color" :icon="item.icon" :url="item.url">
					</tcard>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import tcard from "@/components/tool-card.vue";
	export default {
		components: {
			tcard
		},
		data() {
			return {
				tools: []
			}
		},
		onLoad() {
			this.getDataByOnline();
		},
		methods: {
			getDataByOnline() {
				const that = this;
				uni.request({
					url: 'https://www.lailab.cn/miniprogram/data.php?type=tools',
					success: function(rst) {
						that.tools = rst.data;
					},
				});
			},
		}
	}
</script>

<style>
	.container {
		padding-top: 0;
	}

	.main {
		width: 100%;
		height: auto;
		background-color: #ffffff;
		color: black;
		padding: 10px 0;
	}

	.main-dark {
		background-color: #333;
		color: white;
	}

	.classify-cards {
		width: 94%;
		margin: 0 10px;
	}

	.classify-name {
		display: block;
		height: 30px;
		font-size: 18px;
		line-height: 30px;
		text-indent: 0.5em;
		font-weight: bold;
		clear: both;
	}

	.tool-card {
		width: 47.5%;
		display: inline-block;
		margin-bottom: 15px;
	}

	.tool-card:nth-child(2n-1) {
		margin-right: 5%;
	}
	
	.tool-card:last-child {
		margin-bottom: 0;
	}
</style>
