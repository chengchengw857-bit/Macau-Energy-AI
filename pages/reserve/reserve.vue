<template>
	<view class="page-container">
		<u-navbar title="一站式预约" :autoBack="true" placeholder></u-navbar>
		<view class="section">
			<view class="title">选择预约站点</view>
			<view class="station-brief">
				<image src="https://cdn.uviewui.com/uview/swiper/swiper2.png" class="s-img"></image>
				<view class="s-info">
					<text class="s-n">乐群楼换电站</text>
					<text class="s-a">路环石排湾乐群楼地下</text>
				</view>
			</view>
		</view>

		<view class="section">
			<view class="title">选择到达时间</view>
			<view class="time-grid">
				<view class="time-item" :class="{active: curTime === i}" v-for="i in 6" :key="i" @click="curTime = i">
					<text class="t-val">1{{i}}:00</text>
					<text class="t-status">可选</text>
				</view>
			</view>
		</view>

		<view class="bottom-pay">
			<view class="price-info">
				<text>预约费：</text>
				<text class="p-num">MOP 20</text>
			</view>
			<u-button type="primary" text="立即预约" shape="circle" @click="toSuccess"></u-button>
		</view>
	</view>
</template>

<script>
export default {
	data() { return { curTime: 1 } },
	methods: {
		toSuccess() {
			uni.navigateTo({ url: '/pages/success/success?msg=预约成功&tip=请按时到达，系统已为您预留车位' });
		}
	}
}
</script>

<style lang="scss" scoped>
.page-container { background: #f7f8fa; min-height: 100vh; padding: 20px; }
.section { background: #fff; padding: 20px; border-radius: 16px; margin-bottom: 20px; .title { font-weight: bold; margin-bottom: 15px; } }
.station-brief { display: flex; .s-img { width: 80px; height: 60px; border-radius: 8px; } .s-info { margin-left: 12px; .s-n { font-weight: bold; display: block; } .s-a { font-size: 12px; color: #999; } } }
.time-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; 
	.time-item { border: 1px solid #eee; padding: 10px; border-radius: 8px; text-align: center; 
		&.active { border-color: #2979ff; background: #eef5ff; .t-val { color: #2979ff; } }
		.t-val { font-size: 16px; font-weight: bold; display: block; } .t-status { font-size: 10px; color: #999; }
	}
}
.bottom-pay { position: fixed; bottom: 0; left: 0; right: 0; background: #fff; padding: 20px; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 -5px 15px rgba(0,0,0,0.05); .p-num { font-size: 20px; font-weight: bold; color: #ff9900; } }
</style>