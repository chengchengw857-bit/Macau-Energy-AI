<template>
	<view class="charging-page">
		<u-navbar title="正在加电" :autoBack="true" placeholder border bgColor="#000" leftIconColor="#fff" titleColor="#fff"></u-navbar>
		
		<view class="charge-header">正在加电...</view>
		
		<!-- 动态圆环 (模拟蔚来 UI) -->
		<view class="power-circle">
			<text class="p-num">{{soc}}%</text>
			<text class="p-lab">当前电量</text>
		</view>

		<view class="live-data">
			<view class="d-item"><text class="v">{{voltage}}V</text><text class="l">电压</text></view>
			<view class="d-item"><text class="v">{{power}}kW</text><text class="l">功率</text></view>
			<view class="d-item"><text class="v">MOP {{cost}}</text><text class="l">预估电费</text></view>
		</view>

		<view class="v2g-tips">
			<u-icon name="info-circle" color="#0052D9"></u-icon>
			<text>智慧云端正在优化您的加电效率</text>
		</view>

		<view class="btn-group">
			<u-button type="error" text="停止加电" shape="circle" @click="stop"></u-button>
		</view>
	</view>
</template>

<script>
export default {
	data() { return { soc: 45, voltage: 382, power: 62.5, cost: 12.8, timer: null } },
	onLoad() {
		this.timer = setInterval(() => {
			if(this.soc < 100) {
				this.soc += 1;
				this.cost = (parseFloat(this.cost) + 0.15).toFixed(2);
			}
		}, 3000);
	},
	onUnload() { clearInterval(this.timer); },
	methods: { stop() { uni.showModal({ title: '停止加电', content: '确认结束本次加电?', success: (r) => { if(r.confirm) uni.switchTab({ url: '/pages/index/index' }) } }) } }
}
</script>

<style lang="scss">
.charging-page { background: #000; min-height: 100vh; color: #fff; text-align: center; }
.charge-header { padding-top: 40px; font-size: 18px; letter-spacing: 2px; }
.power-circle { 
	width: 200px; height: 200px; border: 4px solid #0052D9; border-radius: 50%; margin: 50px auto;
	display: flex; flex-direction: column; justify-content: center; box-shadow: 0 0 30px rgba(0,82,217,0.3);
	.p-num { font-size: 48px; font-weight: bold; color: #fff; }
	.p-lab { font-size: 12px; color: #0052D9; }
}
.live-data { display: flex; justify-content: space-around; margin-top: 40px;
	.d-item { .v { font-size: 18px; font-weight: bold; display: block; } .l { font-size: 11px; opacity: 0.5; } }
}
.v2g-tips { margin: 40px; font-size: 12px; color: #0052D9; background: rgba(0,82,217,0.1); padding: 10px; border-radius: 8px; }
.btn-group { padding: 0 60px; }
</style>