<template>
	<view class="rescue-container">
		<!-- 1. 沉浸式地图 -->
		<view class="map-section">
			<map class="main-map" :latitude="latitude" :longitude="longitude" :markers="markers" scale="14"></map>
			<view class="nav-back" @click="back">
				<u-icon name="arrow-left" color="#000" size="20"></u-icon>
			</view>
		</view>

		<!-- 2. 救援控制面板 -->
		<view class="service-panel">
			<view class="handle-bar"></view>
			<view class="ai-status">
				<view class="ai-tag">
					<view class="dot"></view>AI 智慧调度引擎
				</view>
				<text class="eta-text">附近有 2 辆补给车 · 预计 15 分钟抵达</text>
			</view>

			<!-- 一键呼叫按钮 -->
			<view class="main-call-btn" @click="callRescue">
				<view class="icon-circle">
					<u-icon name="integral-fill" color="#fff" size="30"></u-icon>
				</view>
				<view class="btn-txt">
					<text class="t1">一键呼叫应急补给</text>
					<text class="t2">含小电瓶搭电 · 馈电快速回血</text>
				</view>
			</view>

			<!-- 3. 专业救援服务矩阵 (重点修复部分) -->
			<view class="more-section">
				<view class="s-title">更多专业救援服务</view>
				<u-grid :border="false" col="4">
					<u-grid-item v-for="(item, index) in rescueList" :key="index" customStyle="padding: 12px 0">
						<view class="r-icon">
							<!-- 这里统一使用 uView 内置图标，解决显示文字的问题 -->
							<u-icon :name="item.icon" color="#0052D9" size="26"></u-icon>
						</view>
						<text class="r-text">{{item.title}}</text>
					</u-grid-item>
				</u-grid>
			</view>
		</view>

		<!-- 超级会员横幅 -->
		<view class="vip-banner">
			<view class="v-left">
				<text class="v-t">超级保障会员</text>
				<text class="v-s">畅享全年无限次免费应急补给</text>
			</view>
			<view class="v-btn">立即开通</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				latitude: 22.189,
				longitude: 113.544,
				markers: [{
					id: 1,
					latitude: 22.195,
					longitude: 113.548,
					title: '救援补给车',
					width: 35,
					height: 35,
					iconPath: '/static/uview/example/js.png'
				}],
				// 🚀 核心修复：更换为标准内置图标名
				rescueList: [{
						title: '电瓶搭电',
						icon: 'bolt'
					}, // 换成 bolt (闪电)
					{
						title: '故障拖车',
						icon: 'car-fill'
					}, // 换成 car-fill
					{
						title: '流动补胎',
						icon: 'reload'
					}, // 换成 reload (像轮胎旋转)
					{
						title: '配锁开锁',
						icon: 'lock-fill'
					}, // 保持 lock-fill
					{
						title: '叉车租赁',
						icon: 'order'
					}, // 保持 order
					{
						title: '上门洗车',
						icon: 'eye-fill'
					}, // 保持 eye-fill
					{
						title: '货车维修',
						icon: 'setting-fill'
					}, // 保持 setting-fill
					{
						title: '更多',
						icon: 'more-circle'
					} // 保持 more-circle
				]
			}
		},
		methods: {
			back() {
				uni.navigateBack();
			},
			callRescue() {
				uni.showLoading({
					title: 'AI 正在指派最近车辆'
				});
				setTimeout(() => {
					uni.hideLoading();
					uni.navigateTo({
						url: '/pages/success/success?msg=呼叫成功&tip=莫师傅已携带移动补电仓出发'
					});
				}, 2000);
			}
		}
	}
</script>

<style lang="scss" scoped>
	.rescue-container {
		background: #f7f8fa;
		min-height: 100vh;
	}

	.map-section {
		height: 42vh;
		width: 100%;
		position: relative;

		.main-map {
			width: 100%;
			height: 100%;
		}

		.nav-back {
			position: absolute;
			top: 50px;
			left: 20px;
			width: 40px;
			height: 40px;
			background: #fff;
			border-radius: 50%;
			display: flex;
			align-items: center;
			justify-content: center;
			box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
			z-index: 99;
		}
	}

	.service-panel {
		margin-top: -30px;
		background: #fff;
		border-radius: 30px 30px 0 0;
		position: relative;
		z-index: 10;
		padding: 20px;

		.handle-bar {
			width: 40px;
			height: 5px;
			background: #eee;
			border-radius: 3px;
			margin: 0 auto 15px;
		}

		.ai-status {
			background: #f0f7ff;
			padding: 12px 15px;
			border-radius: 12px;
			margin-bottom: 20px;

			.ai-tag {
				display: flex;
				align-items: center;
				color: #0052D9;
				font-size: 12px;
				font-weight: bold;

				.dot {
					width: 6px;
					height: 6px;
					background: #0052D9;
					border-radius: 50%;
					margin-right: 8px;
					animation: blink 1s infinite;
				}
			}

			.eta-text {
				font-size: 12px;
				color: #666;
				margin-top: 5px;
				display: block;
			}
		}
	}

	.main-call-btn {
		background: #20c997;
		border-radius: 50px;
		padding: 15px 25px;
		display: flex;
		align-items: center;
		box-shadow: 0 10px 20px rgba(32, 201, 151, 0.3);

		.icon-circle {
			width: 45px;
			height: 45px;
			background: rgba(255, 255, 255, 0.2);
			border-radius: 50%;
			display: flex;
			align-items: center;
			justify-content: center;
		}

		.btn-txt {
			margin-left: 15px;

			.t1 {
				color: #fff;
				font-size: 17px;
				font-weight: bold;
				display: block;
			}

			.t2 {
				color: rgba(255, 255, 255, 0.8);
				font-size: 10px;
				margin-top: 2px;
			}
		}
	}

	.more-section {
		margin-top: 30px;

		.s-title {
			font-size: 15px;
			font-weight: bold;
			margin-bottom: 15px;
			color: #333;
		}

		.r-icon {
			background: #f4f7fa;
			width: 48px;
			height: 48px;
			border-radius: 14px;
			display: flex;
			align-items: center;
			justify-content: center;
			margin-bottom: 8px;
		}

		.r-text {
			font-size: 12px;
			color: #666;
		}
	}

	.vip-banner {
		margin: 20px 0;
		background: linear-gradient(90deg, #20c997, #00d2ff);
		border-radius: 15px;
		padding: 15px 20px;
		display: flex;
		justify-content: space-between;
		align-items: center;

		.v-t {
			color: #fff;
			font-size: 15px;
			font-weight: bold;
		}

		.v-s {
			color: rgba(255, 255, 255, 0.7);
			font-size: 11px;
		}

		.v-btn {
			background: #fff;
			color: #20c997;
			padding: 5px 15px;
			border-radius: 20px;
			font-size: 12px;
			font-weight: bold;
		}
	}

	@keyframes blink {

		0%,
		100% {
			opacity: 1;
		}

		50% {
			opacity: 0.3;
		}
	}

	/* ！！！强制图标不再重叠或变形 ！！！ */
	::v-deep .u-icon {
		position: static !important;
		display: inline-flex !important;
	}
</style>