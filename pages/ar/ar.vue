<template>
	<view class="ar-nav-wrap">
		<!-- 1. 模拟摄像头背景 -->
		<view class="camera-simulation">
			<!-- 科技感：屏幕边缘定位支架 (新增) -->
			<view class="frame-bracket top-left"></view>
			<view class="frame-bracket top-right"></view>
			<view class="frame-bracket bottom-left"></view>
			<view class="frame-bracket bottom-right"></view>

			<!-- 2. 顶部 AR 状态中心 (新增：高大上的核心) -->
			<view class="ar-status-header">
				<view class="status-pill">
					<view class="ai-pulse">
						<view class="pulse-inner"></view>
					</view>
					<text class="status-text">AI VISION · AR 导航中</text>
				</view>
				<view class="sensor-info">
					<text>SENSOR ACTIVE</text>
					<view class="signal-bars">
						<view class="bar" v-for="i in 4" :key="i"></view>
					</view>
				</view>
			</view>

			<!-- 3. 顶部 HUD 仪表盘 (优化：更有金属和玻璃感) -->
			<view class="hud-top">
				<view class="hud-item">
					<text class="hud-val">5.2<text class="unit">km/h</text></text>
					<text class="hud-label">REAL-TIME SPEED</text>
				</view>
				<view class="hud-item shadow-blue">
					<text class="hud-val">82<text class="unit">%</text></text>
					<text class="hud-label">CAR BATTERY</text>
				</view>
			</view>

			<!-- 4. 路径引导卡片 (优化：增加毛玻璃投影) -->
			<view class="guidance-overlay">
				<view class="direction-card">
					<view class="dir-icon-wrap">
						<u-icon name="arrow-upfill" color="#fff" size="26"></u-icon>
					</view>
					<view class="dir-text">
						<text class="main-dir">前方直行 25 米左转</text>
						<text class="sub-dir">正在接近：乐群楼地下停车场 B 区</text>
					</view>
				</view>
			</view>

			<!-- 5. 3D 路径视觉引导 -->
			<view class="path-visual">
				<view class="chevron-flow">
					<view class="chevron" v-for="n in 5" :key="n" :style="{ animationDelay: n * 0.4 + 's' }"></view>
				</view>
			</view>

			<!-- 6. 目标车位锁定 (优化：更严谨的视觉标识) -->
			<view class="target-stall">
				<view class="scan-bracket-sm tl"></view>
				<view class="scan-bracket-sm tr"></view>
				<view class="scan-bracket-sm bl"></view>
				<view class="scan-bracket-sm br"></view>
				<view class="stall-tag">
					<u-icon name="map-fill" color="#fff" size="12"></u-icon>
					<text>DESTINATION: #B-105</text>
				</view>
				<view class="scan-scanner"></view>
			</view>

			<!-- 7. 底部左侧小雷达 -->
			<view class="mini-radar">
				<view class="radar-circle">
					<view class="radar-sweep"></view>
					<view class="point user-p"></view>
					<view class="point target-p"></view>
				</view>
			</view>
		</view>

		<!-- 8. 底部白色控制区 -->
		<view class="ar-footer">
			<view class="footer-hint">
				<u-icon name="info-circle" color="#2979ff" size="14"></u-icon>
				<text>系统已根据 SLAM 技术完成环境初始化</text>
			</view>
			<view class="footer-btns">
				<view class="btn-secondary" @click="back">退出</view>
				<view class="btn-primary" @click="back">我已到达车位</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	methods: {
		back() {
			uni.navigateBack();
		}
	}
}
</script>

<style lang="scss" scoped>
.ar-nav-wrap { height: 100vh; background: #000; overflow: hidden; font-family: 'Helvetica Neue', Helvetica, sans-serif; }

/* 模拟背景：加入滤镜感 */
.camera-simulation {
	height: 82vh; width: 100%; position: relative;
	background: linear-gradient(rgba(0,0,0,0.4), transparent), url('https://images.unsplash.com/photo-1506521781263-d8422e82f27a?w=800&q=80');
	background-size: cover; background-position: center;
}

/* 屏幕支架装饰 */
.frame-bracket {
	position: absolute; width: 30px; height: 30px; border: 2px solid rgba(255,255,255,0.3); z-index: 5;
	&.top-left { top: 40px; left: 20px; border-right: none; border-bottom: none; }
	&.top-right { top: 40px; right: 20px; border-left: none; border-bottom: none; }
	&.bottom-left { bottom: 20px; left: 20px; border-right: none; border-top: none; }
	&.bottom-right { bottom: 20px; right: 20px; border-left: none; border-top: none; }
}

/* AR 状态条 (新增) */
.ar-status-header {
	position: absolute; top: 60px; left: 0; right: 0; display: flex; flex-direction: column; align-items: center; z-index: 20;
	.status-pill {
		background: rgba(0,0,0,0.6); backdrop-filter: blur(10px); padding: 5px 15px; border-radius: 20px;
		display: flex; align-items: center; border: 1px solid rgba(255,255,255,0.1);
		.status-text { color: #fff; font-size: 11px; font-weight: bold; letter-spacing: 1px; }
		.ai-pulse {
			width: 8px; height: 8px; background: #2979ff; border-radius: 50%; margin-right: 10px; position: relative;
			.pulse-inner {
				position: absolute; width: 100%; height: 100%; background: #2979ff; border-radius: 50%;
				animation: pulse-ring 1.5s infinite;
			}
		}
	}
	.sensor-info {
		margin-top: 5px; display: flex; align-items: center;
		text { color: #2979ff; font-size: 8px; font-weight: bold; letter-spacing: 1px; }
		.signal-bars {
			display: flex; margin-left: 5px; gap: 2px;
			.bar { width: 2px; height: 6px; background: #2979ff; border-radius: 1px; }
		}
	}
}

@keyframes pulse-ring { 0% { transform: scale(1); opacity: 1; } 100% { transform: scale(3); opacity: 0; } }

/* HUD 顶部数据 */
.hud-top {
	position: absolute; top: 120px; left: 0; right: 0; display: flex; justify-content: space-between; padding: 0 30px;
	.hud-item {
		text-align: left;
		.hud-val { font-size: 28px; font-weight: 900; color: #fff; .unit { font-size: 10px; margin-left: 4px; opacity: 0.6; } }
		.hud-label { font-size: 9px; color: #2979ff; font-weight: bold; display: block; margin-top: 2px; }
	}
}

/* 引导卡片 */
.guidance-overlay {
	position: absolute; top: 200px; left: 20px; right: 20px;
	.direction-card {
		background: rgba(41, 121, 255, 0.85); backdrop-filter: blur(15px); border-radius: 20px; padding: 18px;
		display: flex; align-items: center; border: 1px solid rgba(255,255,255,0.2); box-shadow: 0 15px 35px rgba(0,0,0,0.4);
		.dir-icon-wrap { width: 44px; height: 44px; background: rgba(255,255,255,0.2); border-radius: 12px; display: flex; align-items: center; justify-content: center; }
		.dir-text { margin-left: 15px; .main-dir { font-size: 19px; font-weight: bold; display: block; color: #fff; } .sub-dir { font-size: 11px; color: rgba(255,255,255,0.7); margin-top: 2px; } }
	}
}

/* 3D 箭头流 */
.path-visual {
	position: absolute; bottom: 80px; left: 50%; transform: translateX(-50%); perspective: 800px;
	.chevron-flow {
		display: flex; flex-direction: column; align-items: center;
		.chevron {
			width: 80px; height: 40px; margin-bottom: 30px;
			clip-path: polygon(50% 0%, 100% 100%, 50% 75%, 0% 100%);
			background: linear-gradient(to top, rgba(41, 121, 255, 0.8), rgba(0, 210, 255, 0.4));
			opacity: 0; transform: rotateX(65deg) translateY(200px);
			animation: chevronFlow 2.5s infinite linear;
		}
	}
}

@keyframes chevronFlow {
	0% { opacity: 0; transform: rotateX(65deg) translateY(300px) scale(1.5); }
	20% { opacity: 0.8; }
	100% { opacity: 0; transform: rotateX(65deg) translateY(-300px) scale(0.4); }
}

/* 车位锁定框 */
.target-stall {
	position: absolute; top: 50%; left: 50%; width: 240px; height: 150px;
	transform: translate(-50%, -10%);
	.scan-bracket-sm { position: absolute; width: 15px; height: 15px; border: 3px solid #2979ff;
		&.tl { top: 0; left: 0; border-right: none; border-bottom: none; }
		&.tr { top: 0; right: 0; border-left: none; border-bottom: none; }
		&.bl { bottom: 0; left: 0; border-right: none; border-top: none; }
		&.br { bottom: 0; right: 0; border-left: none; border-top: none; }
	}
	.stall-tag {
		position: absolute; top: -35px; left: 0; background: #2979ff; padding: 4px 12px;
		border-radius: 4px; font-size: 11px; font-weight: bold; display: flex; align-items: center;
		text { margin-left: 5px; letter-spacing: 1px; }
	}
	.scan-scanner {
		position: absolute; top: 0; left: 0; width: 100%; height: 2px;
		background: linear-gradient(to right, transparent, #fff, transparent);
		box-shadow: 0 0 15px #2979ff; animation: scanAnim 2s infinite ease-in-out;
	}
}

@keyframes scanAnim { 0%, 100% { top: 0%; } 50% { top: 100%; } }

/* 迷你雷达 */
.mini-radar {
	position: absolute; bottom: 30px; left: 30px;
	.radar-circle {
		width: 110px; height: 110px; border-radius: 50%; background: rgba(0,0,0,0.5);
		border: 1.5px solid rgba(41, 121, 255, 0.4); position: relative; backdrop-filter: blur(5px);
		.radar-sweep {
			width: 100%; height: 100%; border-radius: 50%;
			background: conic-gradient(from 0deg, rgba(41, 121, 255, 0.4), transparent 90deg);
			animation: radar-rotate 4s infinite linear;
		}
		.point { position: absolute; border-radius: 50%;
			&.user-p { width: 8px; height: 8px; background: #2979ff; top: 75%; left: 50%; box-shadow: 0 0 10px #2979ff; }
			&.target-p { width: 6px; height: 6px; background: #52c41a; top: 35%; left: 40%; }
		}
	}
}

@keyframes radar-rotate { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }

/* 底部操作 */
.ar-footer {
	height: 18vh; background: #fff; border-radius: 30px 30px 0 0; padding: 25px;
	display: flex; flex-direction: column; justify-content: space-between;
	box-shadow: 0 -10px 30px rgba(0,0,0,0.2);
	.footer-hint { display: flex; align-items: center; justify-content: center; font-size: 12px; color: #999; font-weight: 500; text { margin-left: 6px; } }
	.footer-btns {
		display: flex; justify-content: space-between; gap: 15px;
		.btn-secondary { flex: 1; height: 50px; line-height: 50px; text-align: center; border-radius: 25px; background: #f0f2f5; color: #666; font-size: 15px; font-weight: bold; }
		.btn-primary { flex: 2; height: 50px; line-height: 50px; text-align: center; border-radius: 25px; background: #2979ff; color: #fff; font-size: 15px; font-weight: bold; box-shadow: 0 8px 20px rgba(41, 121, 255, 0.3); }
	}
}
</style>