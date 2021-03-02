<template>
	<view>
		<page-head :title="title"></page-head>
		<view class="uni-common-mt">
			<form>
				<view class="uni-list list-pd">
					<view class="uni-list-cell cell-pd">
						<view class="uni-uploader">
							<view class="uni-uploader-head">
								<view class="uni-uploader-title">点击可预览选好的图片</view>
								<view class="uni-uploader-info">{{imageList.length}}/{{imgCount}}</view>
							</view>
							<view class="uni-uploader-body">
								<view class="uni-uploader__files">
									<block v-for="(image,index) in imageList" :key="index">
										<view class="uni-uploader__file">
											<image src="~@/static/del.png" class="del_icon" @click="delPic(index)"></image>
											<image class="uni-uploader__img" :src="image" :data-src="image" @tap="previewImage"></image>
										</view>
									</block>
									<view class="uni-uploader__input-box" v-if="showChoose">
										<view class="uni-uploader__input" @tap="chooseImage"></view>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</form>
		</view>
	</view>
</template>
<script>
	import permision from "@/common/permission.js"

	export default {
		data() {
			return {
				imageList: [],
				showChoose: true,
				imgCount: 2,
			}
		},

		methods: {
			chooseImage: async function() {
				uni.chooseImage({
					count: 1,
					success: (res) => {
						console.log(res)
						this.imageList = this.imageList.concat(res.tempFilePaths);
						if (this.imageList.length >= this.imgCount) {
							this.showChoose = false
						}
					},
					fail: (err) => {
						console.log(err)
					}
				})
			},
			previewImage: function(e) {
				var current = e.target.dataset.src
				uni.previewImage({
					current: current,
					urls: this.imageList
				})
			},
			delPic: function(index) {
				this.imageList.splice(index, 1)
				if (this.imageList.length <= this.imgCount) {
					this.showChoose = true

				}
			},
			async checkPermission(code) {
				let type = code ? code - 1 : this.sourceTypeIndex;
				let status = permision.isIOS ? await permision.requestIOS(sourceType[type][0]) :
					await permision.requestAndroid(type === 0 ? 'android.permission.CAMERA' :
						'android.permission.READ_EXTERNAL_STORAGE');

				if (status === null || status === 1) {
					status = 1;
				} else {
					uni.showModal({
						content: "没有开启权限",
						confirmText: "设置",
						success: function(res) {
							if (res.confirm) {
								permision.gotoAppSetting();
							}
						}
					})
				}
				return status;
			}
		}
	}
</script>

<style>
	.cell-pd {
		padding: 22rpx 30rpx;
	}

	.list-pd {
		margin-top: 50rpx;
	}

	.del_icon {
		width: 50rpx;
		height: 50rpx;
		position: absolute;
		right: 0;
		top: 0;
		z-index: 9999;
	}

	.uni-uploader__file {
		position: relative;
	}
</style>
