<template>
	<view class="container">
		<view class="uni-common-mt" style="background:#FFF; padding:20rpx;">
			<rich-text :nodes="nodes"></rich-text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nodes: `<div class="div_class">
						  <h1>Title</h1>
						  <p class="p">
							Life is&nbsp;<i>like</i>&nbsp;a box of
							<b>&nbsp;chocolates</b>.
						  </p>
						</div>`
			}
		},
		onLoad(option) {
			this.nodes = option.nodes ? option.nodes : this.nodes;
		},
		methods: {

		}
	}
</script>

<style>

</style>
