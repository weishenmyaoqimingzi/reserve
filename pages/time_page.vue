<template>
	<view class="content">
		<!-- <view class="cell" v-for='(item,index) in list' :key='index'>
			<view class="cell-title">
				{{item.title}}
			</view>
			<view class="cell-detail" @click="selectDatePicker(item.type,index)">
				{{item.detail}}
			</view>
		</view> -->
		<view class="cell">
			<view class="cell-title">
				开始时间
			</view>
			<view class="cell-detail" @click="selectDatePicker('datetime-all',0)">
				{{startTime}}
			</view>
		</view>
		<view class="cell">
			<view class="cell-title">
				结束时间
			</view>
			<view class="cell-detail" @click="selectDatePicker('datetime-all',1)">
				{{endTime}}
			</view>
		</view>
		<view class="cell">
			<view class="cell-title">
				报名开始时间
			</view>
			<view class="cell-detail" @click="selectDatePicker('datetime-all',2)">
				{{applystartTime}}
			</view>
		</view>
		<view class="cell">
			<view class="cell-title">
				报名截止时间
			</view>
			<view class="cell-detail" @click="selectDatePicker('datetime-all',3)">
				{{applyendTime}}
			</view>
		</view>
		<view class="">
			当前时间：{{todayTime}}
		</view>
		<date-time-picker ref='startTime' :indicatorStyle='indicatorStyle' :type='type'  @change='startTimeChange'></date-time-picker>
		<date-time-picker ref='endTime' :indicatorStyle='indicatorStyle' :type='type'  @change='endTimeChange'></date-time-picker>
		<date-time-picker ref='applystartTime' :indicatorStyle='indicatorStyle' :type='type'  @change='applystartTimeChange'></date-time-picker>
		<date-time-picker ref='applyendTime' :indicatorStyle='indicatorStyle' :type='type'  @change='applyendTimeChange'></date-time-picker>
	</view>
</template>

<script>
	import DateTimePicker from '@/components/bory-dateTimePicker/bory-dateTimePicker.vue'
	export default {
		components: {
			DateTimePicker
		},
		data() {
			return {
				/**
				 * date 年月日
				 * year-month 年月
				 * year 年
				 * datetime 年月日 时分
				 * datetime-all 年月日 时分秒
				 * time 时分秒
				 * hour-minute 时分
				 */
				dateString: '',
				type: 'datetime-all',
				
				todayTime:'',  //今天时间
				startTime:'请选择', //开始时间
				endTime:'请选择',  //结束时间
				applystartTime:'请选择', //报名开始时间
				applyendTime:'请选择',  //报名截止时间
			}
		},
		computed: {
			indicatorStyle() {
				return {
					background: 'rgba(255,0,0,.2)',
					height: '40px',
				};
			}
		},
		methods: {
			selectDatePicker(type, index) {
				this.todayTime= this.yearTime('yearTime')
				console.log(this.todayTime)
				this.type = type;
				if(index == 0){
					// 活动开始时间
					this.$refs['startTime'].show();
				}
				if(index ==1){
					// 活动结束时间
					if(this.startTime =='请选择'){
						uni.showToast({
							icon:'none',
							title:'请先选择开始时间'
						})
					}
					else{
						this.$refs['endTime'].show();
					}
				}
				if(index == 2){
					// 报名开始时间
					if(this.endTime =='请选择'){
						uni.showToast({
							icon:'none',
							title:'请先选择结束时间'
						})
					}
					else{
						this.$refs['applystartTime'].show();
					}
				}
				if(index == 3){
					// 报名结束时间
					if(this.applystartTime =='请选择'){
						uni.showToast({
							icon:'none',
							title:'请先选择开始时间'
						})
					}
					else{
						this.$refs['applyendTime'].show();
					}
				}
			},
			startTimeChange(value) {
				console.log(value)
				this.startTime = value
				if(value>this.todayTime){
					this.startTime = value
					console.log('开始时间大于当前时间')
				}
				else{
					uni.showToast({
						icon:'none',
						title:'开始时间不能小于当前时间'
					})
					this.startTime = '请选择'
				}
			},
			endTimeChange(value){
				if(value>this.startTime){
					this.endTime = value
				}
				else{
					uni.showToast({
						icon:'none',
						title:'结束时间不能小于开始时间'
					})
					this.endTime = '请选择'
				}
			},
			applystartTimeChange(value){
				if(value < this.endTime && value>this.startTime){
					this.applystartTime = value
				}
				else{
					uni.showToast({
						icon:'none',
						title:'报名时间不能大于结束时间,小于开始时间'
					})
					this.applystartTime = '请选择'
				}
			},
			applyendTimeChange(value){
				if(value < this.endTime && value>this.startTime){
					this.applyendTime = value
				}
				else{
					uni.showToast({
						icon:'none',
						title:'报名时间不能大于结束时间,小于开始时间'
					})
					this.applyendTime = '请选择'
				}
			},
			yearTime(e) {
				var a = new Date();
				var y = a.getFullYear();
				var m = a.getMonth() + 1;
				m = m < 10 ? "0" + m : m;
				var d = a.getDate();
				d = d < 10 ? "0" + d : d;

				var h = a.getHours();
				h = h < 10 ? "0" + h : h;
				var mm = a.getMinutes();
				mm = mm < 10 ? "0" + mm : mm;
				var s = a.getSeconds();
				s = s < 10 ? "0" + s : s;

				if (e == 'year') {
					return y + '-' + m + '-' + d;
				}
				if (e == 'time') {
					return h + ':' + mm + ':' + s;
				}
				if (e == 'yearTime') {
				
					return y + '-' + m + '-' + d + ' ' + h + ':' + mm + ':' + s;
				}
				return y + '-' + m + '-' + d;
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
</style>
