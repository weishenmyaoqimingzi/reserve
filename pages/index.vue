<template>
	<view class="content">
		<view class="cell" v-for="item in list" @click="openDetail(item.value)" :key="item.value">
			<view class="cell-title">
				<image src="../static/img/star.png" class="starPng"></image>
				<text class="title_name">{{item.name}} </text>
			</view>
			<view class="cell-detail">
				>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
						value: 1,
						name: '在线选座'
					},
					{
						value: 2,
						name: '时间选择'
					},
					{
						value: 3,
						name: '地图选择'
					},
					{
						value: 4,
						name: '图片上传'
					}
				]
			}
		},
		methods: {
			openDetail(index) {
				if (index == 1) {
					uni.navigateTo({
						url: 'reserve_page'
					})
				}
				if (index == 2) {
					uni.navigateTo({
						url: 'time_page'
					})
				}
				if (index == 3) {
					uni.navigateTo({
						url: 'map_page'
					})
				}
				if (index == 4) {
					uni.switchTab({
						url: 'img_page'
					})
				}
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.starPng {
		width: 40rpx;
		height: 40rpx;
	}

	.cell {
		width: 90%;
		margin: 0 auto;
		height: 80rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		font-size: 32rpx;
		border-bottom: 2rpx solid #F1F1F1;
	}

	.cell-title {
		display: flex;
		align-items: center;
	}

	.title_name {
		margin-left: 20rpx;
		font-size: 28rpx;
	}
</style>
