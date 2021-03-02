<template>
	<view class="venue_reserve">
		<view class="reserve_top">
			<view class="top_view">
				<view class="top_date" v-for="(item,index) in weekArray" v-bind:key="index" :class="[index,{top_date_active:weekIndex==index}]"
				 @click="resetSeat(index)">
					<text>{{item.weekDay}}</text>
				</view>
			</view>
		</view>

		<!-- 图例 -->
		<view class="reserve_legend">
			<view class="dp">
				<view class="reserve_legend_view">
					<view class="reserve_legend_view_i bg_one"></view>
					<text class="">可选</text>
				</view>
				<view class="reserve_legend_view">
					<view class="reserve_legend_view_i bg_two"></view>
					<text>占用</text>
				</view>
				<view class="reserve_legend_view">
					<view class="reserve_legend_view_i bg_three"></view>
					<text>选中</text>
				</view>
			</view>
		</view>

		<view class="reserve_middle">
			<scroll-view class="middle_view_scroll" style="height: 100%" scroll-x="true" scroll-y="true">
				<view class="middle_view" :style="{width:middleWidth+'rpx'}">
					<!-- 场地编号 -->
					<view class="mid_site">
						<view class="mid_site_text" v-for="(item,index) in colArray" v-bind:key="index">
							<view>
								{{item.sitename}}
							</view>
						</view>
					</view>
					<view v-for="(item,index) in venueArray" v-bind:key="index" class="mid_row dp">
						<view class="mid_time dp">
							{{starttime+index}}:00
						</view>
						<view v-for="(seat,col) in item" class="mid_col dp" v-bind:key="col">
							<view class="mid_col_v" @click="handleChoose(index,col)" :class="['mid_col',{'unselected-grid':handleshow(index,col,1)},{'selected-grid':handleshow(index,col,2)},{'bought-grid':handleshow(index,col,3)}]">
							</view>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>

		<view class="reserve_bottom">
			<view class="btm_txt">
				<text class="btm_price">总价：</text>
				<text class="btm_nums">¥{{chooseNum*choosePrice}}</text>
			</view>

			<view class="btm_order btm_gray" v-if="showBtn">
				<text class="">提交订单</text>
			</view>
			<view class="btm_order btm_blue" v-if="!showBtn" @click="openPage">
				<text class="">提交订单</text>
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				weekArray: [], // 周时间数组
				weekIndex: 0, // 周时间下标
				colArray: [], //场次列数组，例如：1号场，2号场，3号场
				venueList: [],
				venueArray: [], //场次的二维数组,1为空场次，2为已选场次，3为占用场次，4为空白，即背景色
				venueArrayType: [], //场次的状态记录
				venueRow: 0, //场次行数
				venueCol: 0, //场次列数
				chooseNum: 0, // 已选场次的数量
				choosePrice: '', //价格
				chooseArray: [], // 已选场次的数组
				chooseList: [], // 添加预定的数组
				chooseTime: '', //添加预定的时间
				selectTime: '', //查询预定的时间

				phoneNum: '',
				venueName: '',
				venueid: '',
				typeID: '',
				showBtn: true,
				starttime: 0, //开始时间
				middleWidth: 0
			}
		},
		onLoad(options) {

			this.weekIndex = 0
			this.venueName = 'xxx'
			this.typeID = 3
			this.venueid = 1
			this.phoneNum = 17688886666
			this.venueCol = 2
			this.venueRow = 15

			if (this.venueCol < 6) {
				this.middleWidth = 750
			} else {
				this.middleWidth = 750 + (this.venueCol - 6) * 100
			}

			this.getNum();
			this.getCrossNum();
			this.getWeekTime();
			this.initVenueArray()

			this.chooseTime = this.weekArray[this.weekIndex].dayTime
		},
		methods: {
			// 获取一周日期
			getWeekTime() {
				var showDate = [];
				var newDate = [];
				var siteDate = [];
				for (var i = 0; i < 7; i++) {
					var weekDayArr = ["周日", "周一", "周二", "周三", "周四", "周五", "周六"]
					var myDate = new Date()
					var year = myDate.getFullYear()
					var milliseconds = myDate.getTime() + 1000 * 60 * 60 * 24 * i;
					var newMyDate = new Date(milliseconds);
					var weekDay = newMyDate.getDay();
					var month = newMyDate.getMonth() + 1;
					var day = newMyDate.getDate();
					showDate.push(weekDayArr[weekDay] + "\n" + month + '-' + day)
					newDate.push(year + '' + (month >= 10 ? month : "0" + month) + '' + (day >= 10 ? day : "0" + day))
					siteDate.push(year + '-' + (month >= 10 ? month : "0" + month) + '-' + (day >= 10 ? day : "0" + day))
				}

				for (var i = 0; i < showDate.length; i++) {
					this.weekArray.push({
						weekDay: showDate[i],
						dayTime: newDate[i],
						selectTime: siteDate[i]
					})
				}
			},
			// 获取一天时间
			getNum: function() {
				this.starttime = 7
			},
			// 场地编号
			getCrossNum: function() {
				this.choosePrice = 80
				this.colArray = [{
						sitename: "4号场"
					},
					{
						sitename: "5号场"
					}
				]
			},

			//初始化数组
			initVenueArray: function() {
				let venueArray1 = Array(this.venueRow).fill(0).map(() => Array(this.venueCol).fill({
					type: 1
				}));
				let venueArray2 = Array(this.venueRow).fill(0).map(() => Array(this.venueCol).fill(1));

				this.venueArray = venueArray1;
				this.venueArrayType = venueArray2;
				this.GetSite()
			},
			//调用接口，返回的数组状态置为3
			GetSite: function() {
				this.venueList = '0-0,0-1,0-2,0-3,0-4,0-5,0-6,0-7,1-0,1-1,1-2,1-3,1-4,1-5,1-6,1-7'
				var test = this.venueList.split(',')
				// 初始化已经购买的,状态为3
				for (var item of test) {
					let oldArr = this.venueArrayType[item.split("-")[1]];
					let newArr = oldArr.slice(0);
					newArr[item.split("-")[0]] = 3;
					this.$set(this.venueArrayType, [item.split("-")[1]], newArr)
				}
			},

			//处理选择逻辑
			handleChoose: function(row, col) {
				let price = 0;
				let pickOn = 0;
				let seatValue = this.venueArrayType[row][col];
				let newArray = this.venueArray;
				//如果是已购，直接返回
				if (seatValue == 3) return
				//如果是已选点击后变未选
				if (seatValue === 2) {
					var chooseItem = col + '-' + row;
					for (var i = 0; i < this.chooseArray.length; i++) {
						if (chooseItem == this.chooseArray[i]) {
							this.chooseArray.splice(i, 1)
						}
					}
					this.chooseNum--;
					this.venueArrayType[row][col] = 1
					if (this.chooseNum == 0) {
						this.showBtn = true
					}
				} else if (seatValue === 1 && this.chooseNum < 3) {
					this.chooseNum++;
					this.chooseArray.push(col + '-' + row)
					this.venueArrayType[row][col] = 2
					if (this.chooseNum > 0) {
						this.showBtn = false
					}
				}
				if (this.chooseNum == 3) {
					uni.showToast({
						title: '最多只能选择三个场次',
						duration: 3000,
						icon: 'none'
					})
					return;
				}
				this.venueArray = newArray.slice();
			},

			// 改变数组状态
			handleshow: function(row, col, showkey) {
				if (this.venueArrayType[row][col] == showkey) return true
				return false
			},

			// 重置数组状态
			resetSeat: function(index) {
				this.chooseArray = [];
				this.chooseList = [];
				this.showBtn = true
				this.chooseNum = 0; // 选择数量置为0,价格自动变为0
				this.weekIndex = index; // 根据下标改变class的颜色
				this.chooseTime = this.weekArray[index].dayTime; // 格式化后的时间

				// 将所有的值变为0
				let oldArray = this.venueArrayType.slice();
				for (let i = 0; i < this.venueRow; i++) {
					for (let j = 0; j < this.venueCol; j++) {
						if (oldArray[i][j] !== 4) {
							oldArray[i][j] = 1
						}
					}
				}
				let newArray = oldArray.slice();

				var selectTime = this.weekArray[this.weekIndex].selectTime;

				let siteData = {
					venueid: this.venueid,
					sporttype: this.typeID,
					datetime: selectTime
				}

				this.venueList = '0-0,0-1,0-2,0-3,0-4,0-5,0-6,0-7,1-0,1-1,1-2,1-3,1-4,1-5,1-6,1-7'
				var test = this.venueList.split(',')
				// 初始化已经购买的,状态为3
				for (var item of test) {
					let oldArr = this.venueArrayType[item.split("-")[1]];
					let newArr = oldArr.slice(0);
					newArr[item.split("-")[0]] = 3;
					this.$set(this.venueArrayType, [item.split("-")[1]], newArr)
				}
			},

			// 提交至服务器成功后跳转至订单页面
			openPage: function() {
				console.log(this.chooseArray)
				uni.showToast({
					icon: 'none',
					title: '提交至服务器'
				})
			},
		}
	}
</script>

<style>
	page {
		width: 100%;
		height: 100%;
	}

	.venue_reserve {
		display: flex;
		flex-direction: column;
		width: 100%;
		height: 100%;
	}

	.reserve_top {}

	.top_view {
		display: flex;
		justify-content: space-around;
	}

	.top_date {
		float: left;
		width: 13%;
		height: 35px;
		color: #666;
		background-color: #F5F5F8;
		list-style: none;
		padding-top: 5px;
		padding-bottom: 5px;
		border-color: #ccc;
		border-style: solid;
		border-width: 1px;
		text-align: center;
		font-size: 12px;
	}

	.top_date_active {
		background-color: #FFFFFF;
		border: 1px solid #007BFF;
		color: #007BFF;
	}

	.reserve_legend {
		display: flex;
		margin-top: 20rpx;
		margin-bottom: 20rpx;
	}

	.dp {
		display: flex;
	}

	/* 图例样式 */
	.reserve_legend_view {
		font-size: 12px;
		display: flex;
		justify-content: center;
		width: 250rpx;
		margin: auto;
	}

	.reserve_legend_view_i {
		width: 30rpx;
		height: 30rpx;
		margin-right: 20rpx;
	}

	.bg_one {
		background-color: #6acb6c;
	}

	.bg_two {
		background-color: #CCCCCC;
	}

	.bg_three {
		background-color: #007BFF;
	}

	/* 图例样式 */

	.reserve_middle {
		flex: 1;
		position: relative;
	}

	.middle_view_scroll {
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
	}

	.middle_view {}

	.mid_site {
		height: 36rpx;
		margin-top: 16rpx;
		margin-bottom: 16rpx;
		margin-left: 95rpx;
	}

	.mid_site_text {
		margin-left: 20px;
		float: left;
		font-size: 12px;
	}

	.mid_row {
		overflow: hidden;
	}

	.mid_time {
		width: 110rpx;
		margin-top: 10rpx;
		justify-content: center;
	}

	.mid_col {
		width: 53px;
		height: 31px;
		justify-content: center;
		align-items: center;
	}

	.mid_col_v {
		width: 100%;
		height: 100%;
		cursor: pointer;
	}

	.selected-grid {
		background: url(../static/img/pic_blue.png)center center no-repeat;
		background-size: 100% 100%;
	}

	.unselected-grid {
		background: url(../static/img/pic_green.png) center center no-repeat;
		background-size: 100% 100%;
	}

	.bought-grid {
		background: url(../static/img/pic_ccc.png) center center no-repeat;
		background-size: 100% 100%;
	}



	/* 底部提交按钮样式 */
	.reserve_bottom {
		display: flex;
		color: #fff;
		line-height: 100rpx;
		flex: 0 0 100rpx;
	}

	.btm_txt {
		background-color: #FFFFFF;
		width: 70%;
		height: 100rpx;
		line-height: 100rpx;
		display: flex;
		padding-left: 20rpx;
	}

	.btm_price {
		font-size: 16px;
		color: #A5A5A5;
	}

	.btm_nums {
		font-size: 22px;
		color: #FF0000;
	}

	.btm_order {
		display: flex;
		width: 30%;
		line-height: 100rpx;
		justify-content: center;
	}

	.btm_gray {
		background-color: #BCBBBB;
		color: #FFFFFF;
	}

	.btm_blue {
		background-color: #007BFF;
		color: #FFFFFF;
	}

	/* 底部提交按钮样式 */
</style>
