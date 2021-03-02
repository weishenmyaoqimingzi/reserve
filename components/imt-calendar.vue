<template>
	<view class="imt-calendar">
		<view class="calendar-month-wrapper">
			<view class="calendar-icon calendar-icon-leftArrow" :style="{color:current&&color}" @click="current&&current--">&#xe6ab;</view>
			<view class="calendar-month">{{currentTime}}</view>
			<view class="calendar-icon calendar-icon-rightArrow" :style="{color:current == calendar.length-1 || color}" @click="current == calendar.length-1 || current++">&#xe6ab;</view>
		</view>
		<view class="calendar-week-wrapper">
			<view class="calendar-week" v-for="(item,index) in week" :key="index">{{item}}</view>
		</view>
		<swiper class="calendar-date-swiper" :current="current" @change="current = $event.detail.current">
			<swiper-item class="calendar-date-wrapper" v-for="(item,index) in calendar" :key="index">
				<view class="calendar-date" v-for="(value,key) in item" :key="key" :style="{background:value.selected&&color,color:value.selected&&'#fff'}">{{value.date}}</view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				week: ['日', '一', '二', '三', '四', '五', '六'],
				current: '',
				startYear: '',
				startMonth: '',
				calendar: []
			}
		},
		props: {
			selected: {
				type: Array,
				default () {
					return []
				}
			},
			color: {
				type: String,
				default: '#e5373f'
			}
		},
		created() {
			let year = new Date().getFullYear()
			let month = new Date().getMonth()
			this.startYear = this.selected.length ? this.selected[0].substr(0, 4) : `${year}`
			this.startMonth = this.selected.length ? this.selected[0].substr(5, 2) - 1 : month
			this.calendar = [...Array((year - this.startYear) * 12 + month - this.startMonth + 1).keys()].map(i => this.getDate(
				this.format(i)))
			this.selected.forEach(i => {
				let time = i.split('-')
				this.calendar[time[1] - this.startMonth - 1 + (time[0] - this.startYear) * 12][time[2] - 1 + new Date(
					`${time[0]}-${time[1]}`).getDay()].selected = true
			})
			this.current = this.calendar.length - 1
		},
		methods: {
			getDate(e) {
				let time = e.split('-')
				return [...Array(new Date(`${time[0]}-${time[1]}`).getDay())].map(i => ({
					date: ''
				})).concat([...Array([4, 6, 9, 11].includes(time[1] * 1) ? 30 : (time[1] != 2 ? 31 : (time[0] % 4 == 0 ? 29 : 28)))
					.keys()
				].map(i => ({
					date: i + 1
				})))
			},
			format(e) {
				let time = new Date(new Date(this.startYear).setMonth(this.startMonth + e))
				return `${time.getFullYear()}-${(time.getMonth()<9 && '0')+(time.getMonth()+1)}`
			}
		},
		computed: {
			currentTime() {
				return this.format(this.current)
			}
		}
	}
</script>

<style>
	@font-face {
		font-family: 'imt-calendar';
		src: url('//at.alicdn.com/t/font_1114123_r6yq558axt.ttf') format('truetype');
	}

	.imt-calendar {
		width: 700upx;
		margin: auto;
		background: #fff;
		border-radius: 10upx;
		text-align: center;
		color: #333;
	}

	.calendar-month-wrapper {
		display: flex;
		justify-content: space-between;
		border-bottom: 2upx solid #e5e5e5;
	}

	.calendar-icon {
		font-family: 'imt-calendar';
		width: 100upx;
		font-size: 32upx;
		line-height: 100upx;
		color: #ccc;
	}

	.calendar-month {
		font-size: 32upx;
		line-height: 100upx;
	}

	.calendar-icon-leftArrow {
		transform: rotate(180deg);
	}

	.calendar-week-wrapper {
		display: flex;
	}

	.calendar-week {
		flex: 1;
		font-size: 32upx;
		line-height: 100upx;
	}

	.calendar-date-swiper {
		height: 600upx;
	}

	.calendar-date-wrapper {
		display: flex;
		flex-wrap: wrap;
	}

	.calendar-date {
		width: 60upx;
		height: 60upx;
		font-size: 32upx;
		line-height: 60upx;
		border-radius: 50%;
		margin: 20upx;
	}
</style>
