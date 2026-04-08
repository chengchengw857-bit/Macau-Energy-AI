<template>
	<view class="success-page">
		<view class="icon-box">
			<u-icon name="checkmark-circle-fill" color="#52c41a" size="80"></u-icon>
			<text class="msg">{{msg}}</text>
			<text class="tip">{{tip}}</text>
		</view>
		<view class="btn-box">
			<u-button type="primary" text="返回首页" @click="goHome"></u-button>
		</view>
	</view>
</template>

<script>
export default {
	data() { return { msg: '', tip: '' } },
	onLoad(opt) {
		this.msg = opt.msg || '操作成功';
		this.tip = opt.tip || '感谢您使用“澳”游电能服务';
	},
	methods: { goHome() { uni.switchTab({ url: '/pages/index/index' }) } }
}
</script>

<style>
.success-page { text-align: center; padding-top: 100px; }
.icon-box { display: flex; flex-direction: column; align-items: center; }
.msg { font-size: 24px; font-weight: bold; margin-top: 20px; }
.tip { color: #999; margin-top: 10px; padding: 0 40px; }
.btn-box { margin-top: 60px; padding: 0 40px; }
</style>