<template>
	<view class="takeaway-container">
		<!-- 粒子背景 -->
		<view class="particles-container">
			<view v-for="n in 25" :key="n" class="particle" :class="{'particle-type-1': n % 3 === 0, 'particle-type-2': n % 3 === 1, 'particle-type-3': n % 3 === 2}" :style="{animationDelay: n * 0.1 + 's'}"></view>
			<!-- 科技感几何形状 -->
			<view class="tech-shape tech-circle"></view>
			<view class="tech-shape tech-square"></view>
			<view class="tech-shape tech-triangle"></view>
			<!-- 粒子连接线 -->
			<view class="particle-connections"></view>
		</view>
		<u-navbar title="换电外卖小哥" :autoBack="true" placeholder bgColor="#0a1128">
			<view class="ai-badge" slot="right">
				<u-icon name="robot" size="16" color="#2979ff"></u-icon>
				<text>AI智能调度</text>
			</view>
		</u-navbar>
		
		<!-- 科技感顶部横幅 -->
		<view class="banner-card">
			<view class="banner-bg">
				<view class="bg-pattern"></view>
				<view class="flare"></view>
				<!-- AI路径规划可视化 -->
				<view class="path-visualization">
					<view class="path-line path-optimal">
						<view class="path-dot start"></view>
						<view class="path-dot middle"></view>
						<view class="path-dot middle"></view>
						<view class="path-dot end"></view>
					</view>
					<view class="path-line path-alternative">
						<view class="path-dot"></view>
						<view class="path-dot"></view>
						<view class="path-dot"></view>
						<view class="path-dot"></view>
					</view>
				</view>
			</view>
			<view class="banner-content">
				<view class="banner-txt">您只管出发，能量我们送达</view>
				<view class="ai-path-info">
					<view class="ai-path-item">
						<u-icon name="map" size="14" color="#2979ff"></u-icon>
						<text>智能路径规划</text>
					</view>
					<view class="ai-path-item">
						<u-icon name="clock" size="14" color="#52c41a"></u-icon>
						<text>预计30分钟到达</text>
					</view>
					<view class="ai-path-item">
						<u-icon name="battery-full" size="14" color="#faad14"></u-icon>
						<text>满电续航保障</text>
					</view>
				</view>
				<!-- AI路径推荐选项 -->
				<view class="ai-path-options">
					<view class="path-option active">
						<text>最快路线</text>
						<text class="path-time">30分钟</text>
					</view>
					<view class="path-option">
						<text>最经济路线</text>
						<text class="path-time">35分钟</text>
					</view>
					<view class="path-option">
						<text>最环保路线</text>
						<text class="path-time">38分钟</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 信息卡片：毛玻璃效果 -->
		<view class="form-card">
			<view class="card-header">
				<text class="card-title">服务信息</text>
				<view class="ai-analysis-badge">
					<u-icon name="scan" size="12" color="#2979ff"></u-icon>
					<text>AI智能分析</text>
				</view>
			</view>
			<u-cell-group :border="false">
				<u-cell 
					icon="map-fill" 
					title="当前车辆位置" 
					value="澳门居雅大厦南面停车场" 
					:titleStyle="{fontWeight:'bold'}"
					:valueStyle="{color: '#666'}"
				></u-cell>
				<u-cell 
					icon="car-fill" 
					title="预约车辆" 
					value="MP-66-88 (Tesla)" 
					:titleStyle="{fontWeight:'bold'}"
					:valueStyle="{color: '#666'}"
				></u-cell>
				<u-cell 
					icon="clock-fill" 
					title="期望服务时间" 
					value="立即出发 (预计30分钟到达)" 
					:titleStyle="{fontWeight:'bold'}"
					:valueStyle="{color: '#666'}"
				></u-cell>
			</u-cell-group>
		</view>

		<!-- 换电员状态跟踪 -->
		<view v-if="showTracker" class="tracker-card">
			<view class="tracker-header">
				<text class="tracker-title">换电员状态</text>
				<view class="status-pulse">
					<u-tag :text="serviceStatus" :type="statusType" size="small"></u-tag>
					<view class="pulse-ring"></view>
				</view>
			</view>
			
			<view class="tracker-content">
				<view class="tracker-info">
					<u-avatar :src="chargerAvatar" size="40"></u-avatar>
					<view class="tracker-details">
						<text class="charger-name">{{chargerName}}</text>
						<text class="charger-vehicle">{{chargerVehicle}}</text>
					</view>
				</view>
				
				<view class="progress-container">
					<view class="progress-bar">
						<view class="progress-fill" :style="{width: progress + '%'}"></view>
						<view class="progress-value">
							<u-count-to :startVal="0" :endVal="progress" :duration="1000" color="#fff" fontSize="12" bold></u-count-to>%
						</view>
					</view>
					<view class="eta">
						<text>预计到达时间：</text>
						<text class="eta-time">{{eta}}</text>
					</view>
				</view>
				
				<view class="map-container">
					<view class="map-placeholder">
						<view class="map-icon-container">
							<u-icon name="map" size="40" color="#2979ff"></u-icon>
							<view class="map-glow"></view>
						</view>
						<text class="map-text">实时位置地图</text>
						<text class="map-subtext">{{chargerLocation}}</text>
						<view class="map-path">
							<view class="path-dot"></view>
							<view class="path-dot"></view>
							<view class="path-dot"></view>
						</view>
					</view>
				</view>
			</view>
		</view>

		<!-- AI智能分析模块 -->
		<view class="ai-analysis-card">
			<view class="card-header">
				<text class="card-title">AI智能分析</text>
				<view class="ai-badge-small">
					<u-icon name="scan" size="12" color="#2979ff"></u-icon>
					<text>实时分析</text>
				</view>
			</view>
			<view class="ai-analysis-content">
				<view class="ai-analysis-item">
					<view class="ai-icon-container">
						<u-icon name="battery-full" size="24" color="#52c41a"></u-icon>
						<view class="ai-icon-glow"></view>
					</view>
					<view class="ai-item-info">
						<text class="ai-item-title">电池健康状态</text>
						<text class="ai-item-value">{{batteryHealth}}%</text>
						<text class="ai-item-desc">良好</text>
					</view>
				</view>
				<view class="ai-analysis-item">
					<view class="ai-icon-container">
						<u-icon name="trending-up" size="24" color="#faad14"></u-icon>
						<view class="ai-icon-glow"></view>
					</view>
					<view class="ai-item-info">
						<text class="ai-item-title">剩余续航</text>
						<text class="ai-item-value">{{remainingRange}}km</text>
						<text class="ai-item-desc">预计可行驶</text>
					</view>
				</view>
				<view class="ai-analysis-item">
					<view class="ai-icon-container">
						<u-icon name="clock" size="24" color="#2979ff"></u-icon>
						<view class="ai-icon-glow"></view>
					</view>
					<view class="ai-item-info">
						<text class="ai-item-title">最佳换电时间</text>
						<text class="ai-item-value">{{bestSwapTime}}</text>
						<text class="ai-item-desc">AI推荐</text>
					</view>
				</view>
				<view class="ai-analysis-item">
					<view class="ai-icon-container">
						<u-icon name="trending-down" size="24" color="#13c2c2"></u-icon>
						<view class="ai-icon-glow"></view>
					</view>
					<view class="ai-item-info">
						<text class="ai-item-title">24小时电量预测</text>
						<text class="ai-item-value">85% → 30%</text>
						<text class="ai-item-desc">基于历史使用模式</text>
					</view>
				</view>
				<view class="ai-analysis-item">
					<view class="ai-icon-container">
						<u-icon name="map" size="24" color="#722ed1"></u-icon>
						<view class="ai-icon-glow"></view>
					</view>
					<view class="ai-item-info">
						<text class="ai-item-title">推荐充电站</text>
						<text class="ai-item-value">澳门新口岸</text>
						<text class="ai-item-desc">距离1.2km，空闲充电桩多</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 上门补电服务卡片 -->
		<view class="door-to-door-service-card">
			<view class="service-card-header">
				<text class="service-card-title">上门补电服务</text>
				<view class="service-badge">
					<u-icon name="plus" size="12" color="#52c41a"></u-icon>
					<text>AI智能调度</text>
				</view>
			</view>
			<view class="service-card-content">
				<view class="service-item">
					<view class="service-icon-container">
						<u-icon name="lightbulb" size="28" color="#faad14"></u-icon>
						<view class="service-icon-glow"></view>
					</view>
					<view class="service-item-info">
						<text class="service-item-title">救援充电</text>
						<text class="service-item-desc">充电紧急救援</text>
					</view>
					<view class="go-button" @click="openDoorToDoorService">
						<text>GO</text>
						<u-icon name="arrow-right" size="14" color="#fff"></u-icon>
					</view>
				</view>
			</view>
		</view>

		<!-- 服务说明 -->
		<view class="notice">
			<u-icon name="info-circle" color="#2979ff" size="16"></u-icon>
			<text>服务说明：换电员将驾驶移动充电车前往您的所在地。</text>
		</view>

		<!-- 底部按钮 -->
		<view class="footer">
			<view class="footer-main">
				<u-button 
					v-if="!showTracker"
					type="primary" 
					text="呼叫换电小哥" 
					shape="circle" 
					@click="callService"
					:plain="false"
					size="large"
					theme="gradient"
					gradientColor="['#2979ff', '#0052d9']"
					hover-class="btn-hover"
					:disabled="isButtonDisabled"
					:loading="isLoading"
					loadingText="处理中..."
				></u-button>
				
				<view v-else class="footer-actions">
					<u-button 
						type="default" 
						text="取消服务" 
						shape="circle" 
						size="large"
						hover-class="btn-hover"
						@click="cancelService"
					></u-button>
					<u-button 
						type="primary" 
						text="联系换电员" 
						shape="circle" 
						size="large"
						theme="gradient"
						gradientColor="['#2979ff', '#0052d9']"
						hover-class="btn-hover"
						@click="contactCharger"
					></u-button>
				</view>
			</view>
			
			<!-- AR导航入口 -->
			<view class="ar-navigation-entry">
				<view class="ar-button" @click="openARNavigation">
					<u-icon name="scan" size="24" color="#2979ff"></u-icon>
					<text>AR导航</text>
					<view class="ar-glow"></view>
				</view>
				<text class="ar-desc">使用AR技术找到附近换电站</text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			isLoading: false,
			isButtonDisabled: false,
			showTracker: false,
			serviceStatus: '待派单',
			statusType: 'info',
			chargerName: '莫小哥',
			chargerAvatar: 'https://images.unsplash.com/photo-1535713875002-d1d0cf377fde?w=100&q=80',
			chargerVehicle: '移动充电车 MP-88-66',
			chargerLocation: '澳门半岛 - 友谊大马路',
			eta: '25分钟',
			progress: 20,
			locationTimer: null,
			progressTimer: null,
			// AI智能分析数据
			batteryHealth: 85,
			remainingRange: 120,
			bestSwapTime: '今天 14:30'
		}
	},
	methods: {
		callService() {
			if (this.isLoading) return;
			
			this.isLoading = true;
			this.isButtonDisabled = true;
			
			// 显示派单状态流程
			const statusFlow = [
				{ title: '正在派单...', duration: 1000 },
				{ title: '正在联系换电员...', duration: 1500 },
				{ title: '换电员确认中...', duration: 1000 },
				{ title: '换电员已接单...', duration: 1000 },
				{ title: '换电员正在取电池...', duration: 1500 },
				{ title: '换电员已出发...', duration: 1000 }
			];
			
			let currentStatus = 0;
			
			const showNextStatus = () => {
				if (currentStatus < statusFlow.length) {
					uni.showLoading({ 
						title: statusFlow[currentStatus].title,
						mask: true
					});
					
					setTimeout(() => {
						currentStatus++;
						showNextStatus();
					}, statusFlow[currentStatus].duration);
				} else {
					uni.hideLoading();
					this.isLoading = false;
					this.isButtonDisabled = false;
					
					// 显示成功提示
					uni.showToast({
						title: '派单成功',
						icon: 'success',
						duration: 1500
					});
					
					setTimeout(() => {
						this.showTracker = true;
						this.serviceStatus = '前往中';
						this.statusType = 'success';
						this.startLocationTracking();
						this.startProgressUpdate();
					}, 1500);
				}
			};
			
			// 开始显示状态流程
			showNextStatus();
		},
		
		startLocationTracking() {
			// 模拟实时位置更新
			const locations = [
				'澳门半岛 - 友谊大马路',
				'澳门半岛 - 葡京路',
				'澳门半岛 - 南湾大马路',
				'澳门半岛 - 西湾大桥',
				'氹仔 - 连贯公路',
				'氹仔 - 北安大马路',
				'目的地附近'
			];
			
			// 状态变更点
			const statusChanges = [
				{ index: 2, status: '行驶中', type: 'success' },
				{ index: 4, status: '即将到达', type: 'warning' },
				{ index: 6, status: '已到达', type: 'success' }
			];
			
			let index = 0;
			this.locationTimer = setInterval(() => {
				if (index < locations.length) {
					this.chargerLocation = locations[index];
					
					// 检查是否需要更新状态
					const statusChange = statusChanges.find(change => change.index === index);
					if (statusChange) {
						this.serviceStatus = statusChange.status;
						this.statusType = statusChange.type;
						
						// 显示状态变更提示
						if (statusChange.status === '已到达') {
							uni.showToast({
								title: '换电员已到达',
								icon: 'success',
								duration: 1500
							});
						}
					}
					
					index++;
					
					// 更新预计到达时间
					const minutes = 25 - Math.floor(index * 3.5);
					this.eta = minutes > 0 ? minutes + '分钟' : '即将到达';
				} else {
					clearInterval(this.locationTimer);
					this.serviceStatus = '已到达';
					this.statusType = 'success';
					this.eta = '已到达';
					this.progress = 100;
					
					// 显示服务完成提示
					setTimeout(() => {
						uni.showToast({
							title: '换电服务已完成',
							icon: 'success',
							duration: 1500
						});
						
						// 显示支付和评价选项
						setTimeout(() => {
							this.showPaymentAndRating();
						}, 2000);
					},
					1000);
				}
			}, 5000);
		},
		
		startProgressUpdate() {
			// 模拟进度更新
			this.progressTimer = setInterval(() => {
				if (this.progress < 100) {
					this.progress += 2;
				} else {
					clearInterval(this.progressTimer);
				}
			}, 1000);
		},
		
		cancelService() {
			uni.showModal({
				title: '取消服务',
				content: '确定要取消本次换电服务吗？',
				confirmText: '确定',
				cancelText: '取消',
				success: (res) => {
					if (res.confirm) {
						// 清除定时器
						if (this.locationTimer) clearInterval(this.locationTimer);
						if (this.progressTimer) clearInterval(this.progressTimer);
						
						this.showTracker = false;
						this.serviceStatus = '待派单';
						this.statusType = 'info';
						this.progress = 0;
						
						uni.showToast({
							title: '服务已取消',
							icon: 'success',
							duration: 1500
						});
					}
				}
			});
		},
		
		contactCharger() {
			uni.showActionSheet({
				itemList: ['电话联系', '发送消息'],
				success: (res) => {
					if (res.tapIndex === 0) {
						// 模拟拨打电话
						uni.showToast({
							title: '正在拨打莫小哥电话...',
							icon: 'none',
							duration: 2000
						});
					} else if (res.tapIndex === 1) {
						// 模拟发送消息
						uni.showToast({
							title: '打开消息界面',
							icon: 'none',
							duration: 2000
						});
					}
				}
			});
		},
		
		showPaymentAndRating() {
			// 显示支付和评价选项
			uni.showModal({
				title: '服务完成',
				content: '换电服务已完成，是否查看费用明细并评价？',
				confirmText: '查看并评价',
				cancelText: '稍后',
				success: (res) => {
					if (res.confirm) {
						// 显示费用明细
						this.showPaymentDetails();
					}
				}
			});
		},
		
		showPaymentDetails() {
			// 模拟费用明细
			const paymentDetails = {
				batteryFee: 80,
				serviceFee: 30,
				deliveryFee: 20,
				total: 130
			};
			
			uni.showModal({
				title: '费用明细',
				content: `电池租赁费: MOP ${paymentDetails.batteryFee}\n服务费用: MOP ${paymentDetails.serviceFee}\n配送费用: MOP ${paymentDetails.deliveryFee}\n\n总计: MOP ${paymentDetails.total}`,
				confirmText: '确认支付',
				cancelText: '稍后支付',
				success: (res) => {
					if (res.confirm) {
						// 模拟支付过程
						uni.showLoading({ title: '正在支付...', mask: true });
						setTimeout(() => {
							uni.hideLoading();
							uni.showToast({ title: '支付成功', icon: 'success', duration: 1500 });
							
							// 显示评价界面
							setTimeout(() => {
								this.showRatingForm();
							}, 1500);
						}, 2000);
					}
				}
			});
		},
		
		showRatingForm() {
			// 模拟评价界面
			uni.showActionSheet({
				itemList: ['非常满意', '满意', '一般', '不满意'],
				success: (res) => {
					const ratings = ['非常满意', '满意', '一般', '不满意'];
					const rating = ratings[res.tapIndex];
					
					uni.showToast({
						title: `感谢您的${rating}评价`,
						icon: 'success',
						duration: 1500
					});
					
					// 重置服务状态
					setTimeout(() => {
						this.showTracker = false;
						this.serviceStatus = '待派单';
						this.statusType = 'info';
						this.progress = 0;
					},
					2000);
				}
			});
		}
	},
	
	// 打开上门补电服务
	openDoorToDoorService() {
		uni.showModal({
			title: '上门补电服务',
			content: '是否启动上门补电服务？我们将安排专业充电团队前往您的位置。',
			confirmText: '立即呼叫',
			cancelText: '稍后',
			success: (res) => {
				if (res.confirm) {
					uni.showToast({
						title: '已提交上门补电请求',
						icon: 'success',
						duration: 2000
					});
				}
			}
		});
	},
	
	// 打开AR导航
	openARNavigation() {
		// 跳转到AR导航页面
		uni.navigateTo({
			url: '/pages/ar/ar'
		});
	},
	
	beforeUnmount() {
		// 清除定时器
		if (this.locationTimer) clearInterval(this.locationTimer);
		if (this.progressTimer) clearInterval(this.progressTimer);
	}
}
</script>

<style lang="scss" scoped>
.takeaway-container { 
	padding: 20px; 
	background: linear-gradient(135deg, #f4f7fa 0%, #e8f0fe 100%); 
	min-height: 100vh; 
	position: relative;
	overflow: hidden;
}

/* 粒子背景 */
.particles-container {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	pointer-events: none;
	z-index: 1;
}

/* AI徽章 */
.ai-badge {
	display: flex;
	align-items: center;
	gap: 4px;
	background: rgba(41, 121, 255, 0.1);
	padding: 4px 8px;
	border-radius: 12px;
	border: 1px solid rgba(41, 121, 255, 0.3);
	
	text {
		font-size: 11px;
		color: #2979ff;
		font-weight: bold;
	}
}

/* 粒子效果 */
.particle {
	position: absolute;
	width: 2px;
	height: 2px;
	background: rgba(41, 121, 255, 0.6);
	border-radius: 50%;
	animation: float 6s infinite ease-in-out;
	box-shadow: 0 0 6px rgba(41, 121, 255, 0.8);
}

/* 不同类型的粒子 */
.particle-type-1 {
	width: 3px;
	height: 3px;
	background: rgba(41, 121, 255, 0.8);
	animation-duration: 8s;
	box-shadow: 0 0 8px rgba(41, 121, 255, 1);
}

.particle-type-2 {
	width: 1px;
	height: 1px;
	background: rgba(0, 255, 200, 0.6);
	animation-duration: 10s;
	animation-delay: 0.5s;
	box-shadow: 0 0 6px rgba(0, 255, 200, 0.8);
}

.particle-type-3 {
	width: 4px;
	height: 4px;
	background: rgba(120, 120, 255, 0.7);
	animation-duration: 12s;
	animation-delay: 1s;
	box-shadow: 0 0 10px rgba(120, 120, 255, 0.9);
}

/* 科技感几何形状 */
.tech-shape {
	position: absolute;
	opacity: 0.1;
	animation: tech-shape-animation 20s infinite ease-in-out;
}

.tech-circle {
	top: 20%;
	right: 10%;
	width: 120px;
	height: 120px;
	border: 2px solid rgba(41, 121, 255, 0.5);
	border-radius: 50%;
	animation-delay: 2s;
}

.tech-square {
	top: 60%;
	left: 5%;
	width: 80px;
	height: 80px;
	border: 2px solid rgba(0, 255, 200, 0.5);
	animation-delay: 4s;
}

.tech-triangle {
	top: 40%;
	left: 15%;
	width: 0;
	height: 0;
	border-left: 40px solid transparent;
	border-right: 40px solid transparent;
	border-bottom: 70px solid rgba(120, 120, 255, 0.5);
	animation-delay: 6s;
}

/* 粒子连接线 */
.particle-connections {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	opacity: 0.3;
	background: radial-gradient(circle at 50% 50%, rgba(41, 121, 255, 0.1), transparent 70%);
	animation: connection-pulse 8s infinite alternate;
}

@keyframes float {
	0% {
		transform: translateY(100vh) translateX(0) scale(0);
		opacity: 0;
	}
	10% {
		opacity: 1;
	}
	90% {
		opacity: 1;
	}
	100% {
		transform: translateY(-100px) translateX(100px) scale(1);
		opacity: 0;
	}
}

@keyframes tech-shape-animation {
	0% {
		transform: rotate(0deg) scale(1);
		opacity: 0.1;
	}
	50% {
		transform: rotate(180deg) scale(1.2);
		opacity: 0.3;
	}
	100% {
		transform: rotate(360deg) scale(1);
		opacity: 0.1;
	}
}

@keyframes connection-pulse {
	0% {
		transform: scale(1);
		opacity: 0.2;
	}
	100% {
		transform: scale(1.1);
		opacity: 0.4;
	}
}

/* 科技感横幅 */
.banner-card { 
	position: relative; 
	height: 180px; 
	border-radius: 20px; 
	overflow: hidden; 
	margin-bottom: 25px; 
	box-shadow: 0 12px 30px rgba(0,0,0,0.1);
	
	.banner-bg { 
		width: 100%; 
		height: 100%; 
		background: linear-gradient(135deg, #000 0%, #0a1128 100%);
		position: relative;
		
		.bg-pattern { 
			position: absolute; 
			width: 100%; 
			height: 100%; 
			background-image: radial-gradient(circle at 80% 20%, rgba(41, 121, 255, 0.2) 0%, transparent 60%);
		}
		
		.flare { 
			position: absolute; 
			top: 10%; 
			right: 10%; 
			width: 180px; 
			height: 180px; 
			background: radial-gradient(circle, rgba(41, 121, 255, 0.12), transparent 70%);
			animation: breathe 4s infinite alternate;
		}
		
		/* 路径规划可视化 */
		.path-visualization {
			position: absolute;
			top: 50%;
			left: 50%;
			transform: translate(-50%, -50%);
			width: 80%;
			height: 60%;
			opacity: 0.6;
			
			.path-line {
				display: flex;
				align-items: center;
				justify-content: space-between;
				position: relative;
				margin-bottom: 20px;
				
				&::before {
					content: '';
					position: absolute;
					top: 50%;
					left: 0;
					right: 0;
					height: 2px;
					transform: translateY(-50%);
				}
				
				.path-dot {
					width: 8px;
					height: 8px;
					border-radius: 50%;
					animation: pulse 2s infinite alternate;
					
					&.start {
						background: #52c41a;
						box-shadow: 0 0 10px rgba(82, 196, 26, 0.6);
					}
					
					&.middle {
						background: #2979ff;
						box-shadow: 0 0 10px rgba(41, 121, 255, 0.6);
						animation-delay: 0.5s;
					}
					
					&.end {
						background: #faad14;
						box-shadow: 0 0 10px rgba(250, 173, 20, 0.6);
						animation-delay: 1s;
					}
				}
				
				&.path-optimal {
					&::before {
						background: linear-gradient(90deg, #52c41a, #2979ff, #faad14);
					}
					
					.path-dot {
						width: 10px;
						height: 10px;
					}
				}
				
				&.path-alternative {
					&::before {
						background: linear-gradient(90deg, rgba(41, 121, 255, 0.3), rgba(41, 121, 255, 0.1));
					}
					
					.path-dot {
						background: rgba(41, 121, 255, 0.4);
					}
				}
			}
		}
	}
	
	.banner-content {
			position: absolute;
			bottom: 25px;
			left: 25px;
			right: 25px;
			z-index: 10;
		}

		.banner-txt { 
			color: #fff; 
			font-size: 20px; 
			font-weight: bold; 
			text-shadow: 0 2px 8px rgba(0,0,0,0.6);
			animation: fadeInUp 0.8s ease-out;
			margin-bottom: 15px;
		}

		.ai-path-info {
			display: flex;
			gap: 15px;
			animation: fadeInUp 0.8s ease-out 0.2s both;
		}

		.ai-path-item {
				display: flex;
				align-items: center;
				gap: 4px;
				background: rgba(255, 255, 255, 0.1);
				padding: 6px 12px;
				border-radius: 16px;
				backdrop-filter: blur(10px);
				border: 1px solid rgba(255, 255, 255, 0.2);
				
				text {
					font-size: 11px;
					color: #fff;
					font-weight: bold;
				}
			}
			
			/* AI路径推荐选项 */
			.ai-path-options {
				display: flex;
				gap: 10px;
				margin-top: 15px;
				animation: fadeInUp 0.8s ease-out 0.4s both;
				
				.path-option {
					display: flex;
					flex-direction: column;
					align-items: center;
					gap: 4px;
					background: rgba(255, 255, 255, 0.1);
					padding: 8px 12px;
					border-radius: 12px;
					backdrop-filter: blur(10px);
					border: 1px solid rgba(255, 255, 255, 0.2);
					transition: all 0.3s ease;
					cursor: pointer;
					
					text {
						font-size: 10px;
						color: #fff;
						font-weight: bold;
					}
					
					.path-time {
						font-size: 9px;
						color: rgba(255, 255, 255, 0.8);
						font-weight: normal;
					}
					
					&:hover {
						background: rgba(255, 255, 255, 0.15);
						transform: translateY(-2px);
						border-color: rgba(41, 121, 255, 0.5);
					}
					
					&.active {
						background: rgba(41, 121, 255, 0.3);
						border-color: #2979ff;
						box-shadow: 0 0 15px rgba(41, 121, 255, 0.4);
					}
				}
			}
}

/* 信息卡片 */
.form-card { 
	background: rgba(255,255,255,0.9); 
	border-radius: 20px; 
	overflow: hidden; 
	box-shadow: 0 8px 25px rgba(0,0,0,0.05);
	backdrop-filter: blur(10px);
	border: 1px solid rgba(255,255,255,0.2);
	animation: slideInUp 0.6s ease-out;
}

/* AI智能分析模块 */
.ai-analysis-card {
	background: rgba(255,255,255,0.9);
	border-radius: 20px;
	padding: 20px;
	margin: 20px 0;
	box-shadow: 0 8px 25px rgba(0,0,0,0.05);
	backdrop-filter: blur(10px);
	border: 1px solid rgba(255,255,255,0.2);
	animation: slideInUp 0.6s ease-out 0.3s both;
	will-change: transform, opacity;
	transition: all 0.3s ease;
	transform-style: preserve-3d;
	
	&:hover {
		box-shadow: 0 20px 40px rgba(0,0,0,0.15);
		transform: translateY(-5px) rotateX(3deg) rotateY(3deg);
	}
	
	.card-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 20px;
		
		.card-title {
			font-size: 16px;
			font-weight: bold;
			color: #333;
		}
		
		.ai-badge-small {
			display: flex;
			align-items: center;
			gap: 4px;
			background: rgba(41, 121, 255, 0.1);
			padding: 2px 8px;
			border-radius: 10px;
			border: 1px solid rgba(41, 121, 255, 0.3);
			
			text {
				font-size: 10px;
				color: #2979ff;
				font-weight: bold;
			}
		}
	}
	
	.ai-analysis-content {
		display: flex;
		flex-direction: column;
		gap: 15px;
	}
	
	.ai-analysis-item {
		display: flex;
		align-items: center;
		gap: 15px;
		padding: 15px;
		background: rgba(248, 249, 250, 0.8);
		border-radius: 12px;
		transition: all 0.3s ease;
		position: relative;
		overflow: hidden;
		
		&::before {
			content: '';
			position: absolute;
			top: 0;
			left: 0;
			width: 4px;
			height: 100%;
			background: linear-gradient(180deg, #2979ff, #0052d9);
			border-radius: 4px 0 0 4px;
		}
		
		&:hover {
			background: rgba(240, 245, 255, 0.9);
			transform: translateX(5px);
		}
		
		.ai-icon-container {
			position: relative;
			display: flex;
			align-items: center;
			justify-content: center;
			width: 48px;
			height: 48px;
			background: rgba(41, 121, 255, 0.1);
			border-radius: 12px;
			
			.ai-icon-glow {
				position: absolute;
				top: 50%;
				left: 50%;
				transform: translate(-50%, -50%);
				width: 60px;
				height: 60px;
				background: radial-gradient(circle, rgba(41, 121, 255, 0.3), transparent 70%);
				border-radius: 50%;
				animation: glow-pulse 2s infinite alternate;
			}
		}
		
		.ai-item-info {
			flex: 1;
			
			.ai-item-title {
				font-size: 14px;
				color: #666;
				display: block;
				margin-bottom: 4px;
			}
			
			.ai-item-value {
				font-size: 18px;
				font-weight: bold;
				color: #333;
				display: block;
				margin-bottom: 2px;
				animation: pulse 2s infinite alternate;
			}
			
			.ai-item-desc {
				font-size: 12px;
				color: #999;
			}
		}
	}
}

/* 上门补电服务卡片 */
.door-to-door-service-card {
	background: rgba(255,255,255,0.9);
	border-radius: 20px;
	padding: 20px;
	margin: 20px 0;
	box-shadow: 0 8px 25px rgba(0,0,0,0.05);
	backdrop-filter: blur(10px);
	border: 1px solid rgba(255,255,255,0.2);
	animation: slideInUp 0.6s ease-out 0.4s both;
	will-change: transform, opacity;
	transition: all 0.3s ease;
	transform-style: preserve-3d;
	
	&:hover {
		box-shadow: 0 20px 40px rgba(0,0,0,0.15);
		transform: translateY(-5px) rotateX(3deg) rotateY(3deg);
	}
	
	.service-card-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 20px;
		
		.service-card-title {
			font-size: 16px;
			font-weight: bold;
			color: #333;
		}
		
		.service-badge {
			display: flex;
			align-items: center;
			gap: 4px;
			background: rgba(82, 196, 26, 0.1);
			padding: 2px 8px;
			border-radius: 10px;
			border: 1px solid rgba(82, 196, 26, 0.3);
			
			text {
				font-size: 10px;
				color: #52c41a;
				font-weight: bold;
			}
		}
	}
	
	.service-card-content {
		display: flex;
		flex-direction: column;
		gap: 15px;
	}
	
	.service-item {
		display: flex;
		align-items: center;
		gap: 15px;
		padding: 15px;
		background: linear-gradient(135deg, rgba(248, 249, 250, 0.8), rgba(255, 255, 255, 0.9));
		border-radius: 12px;
		transition: all 0.3s ease;
		position: relative;
		overflow: hidden;
		
		&::before {
			content: '';
			position: absolute;
			top: 0;
			left: 0;
			width: 4px;
			height: 100%;
			background: linear-gradient(180deg, #faad14, #ff7a45);
			border-radius: 4px 0 0 4px;
		}
		
		&:hover {
			background: rgba(255, 250, 240, 0.95);
			transform: translateX(5px);
			box-shadow: 0 10px 20px rgba(250, 173, 20, 0.1);
		}
		
		.service-icon-container {
			position: relative;
			display: flex;
			align-items: center;
			justify-content: center;
			width: 56px;
			height: 56px;
			background: linear-gradient(135deg, rgba(250, 173, 20, 0.1), rgba(250, 173, 20, 0.2));
			border-radius: 12px;
			
			.service-icon-glow {
				position: absolute;
				top: 50%;
				left: 50%;
				transform: translate(-50%, -50%);
				width: 70px;
				height: 70px;
				background: radial-gradient(circle, rgba(250, 173, 20, 0.3), transparent 70%);
				border-radius: 50%;
				animation: glow-pulse 2s infinite alternate;
			}
		}
		
		.service-item-info {
			flex: 1;
			
			.service-item-title {
				font-size: 15px;
				font-weight: bold;
				color: #333;
				display: block;
				margin-bottom: 4px;
			}
			
			.service-item-desc {
				font-size: 12px;
				color: #999;
			}
		}
		
		.go-button {
			display: flex;
			align-items: center;
			gap: 4px;
			padding: 8px 16px;
			background: linear-gradient(135deg, #faad14, #ff7a45);
			border-radius: 20px;
			box-shadow: 0 4px 12px rgba(250, 173, 20, 0.3);
			transition: all 0.3s ease;
			cursor: pointer;
			
			text {
				font-size: 14px;
				font-weight: bold;
				color: #fff;
			}
			
			&:hover {
				transform: scale(1.05);
				box-shadow: 0 6px 16px rgba(250, 173, 20, 0.4);
			}
		}
	}
}

/* 服务说明 */
.notice { 
	margin-top: 20px; 
	padding: 15px; 
	background: rgba(41,121,255,0.05); 
	border-radius: 12px; 
	color: #2979ff; 
	font-size: 13px; 
	display: flex; 
	align-items: flex-start; 
	text { 
		margin-left: 8px; 
		line-height: 1.4;
	}
	animation: fadeIn 0.8s ease-out 0.4s both;
}

/* 底部按钮 */
.footer { 
	position: fixed; 
	bottom: 40px; 
	left: 20px; 
	right: 20px; 
	display: flex;
	flex-direction: column;
	gap: 15px;
}

/* 底部主按钮区域 */
.footer-main {
	width: 100%;
}

/* 底部操作按钮 */
.footer-actions {
	display: flex;
	justify-content: space-between;
	gap: 15px;
	
	.u-button {
		flex: 1;
	}
}

/* AR导航入口 */
.ar-navigation-entry {
	display: flex;
	align-items: center;
	justify-content: center;
	flex-direction: column;
	gap: 8px;
	animation: fadeInUp 1s ease-out 0.8s both;
	
	.ar-button {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 6px;
		padding: 12px 24px;
		background: rgba(255, 255, 255, 0.9);
		border-radius: 16px;
		border: 1px solid rgba(41, 121, 255, 0.3);
		box-shadow: 0 8px 20px rgba(0,0,0,0.05);
		transition: all 0.3s ease;
		cursor: pointer;
		position: relative;
		overflow: hidden;
		
		text {
			font-size: 14px;
			font-weight: bold;
			color: #2979ff;
		}
		
		.ar-glow {
			position: absolute;
			top: 50%;
			left: 50%;
			transform: translate(-50%, -50%);
			width: 100px;
			height: 100px;
			background: radial-gradient(circle, rgba(41, 121, 255, 0.3), transparent 70%);
			border-radius: 50%;
			animation: glow-pulse 2s infinite alternate;
		}
		
		&:hover {
			transform: translateY(-4px) scale(1.05);
			box-shadow: 0 12px 25px rgba(41, 121, 255, 0.3);
			border-color: #2979ff;
			background: rgba(255, 255, 255, 1);
		}
	}
	
	.ar-desc {
		font-size: 11px;
		color: #666;
		text-align: center;
		max-width: 200px;
		line-height: 1.3;
	}
}

/* 换电员状态跟踪卡片 */
.tracker-card {
	background: rgba(255,255,255,0.9);
	border-radius: 20px;
	padding: 20px;
	margin: 20px 0;
	box-shadow: 0 8px 25px rgba(0,0,0,0.05);
	backdrop-filter: blur(10px);
	border: 1px solid rgba(255,255,255,0.2);
	animation: slideInUp 0.6s ease-out;
}

.tracker-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 15px;
	
	.tracker-title {
		font-size: 16px;
		font-weight: bold;
		color: #333;
	}
	
	.status-pulse {
		position: relative;
		display: inline-block;
	}
	
	.pulse-ring {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		width: 100%;
		height: 100%;
		border: 2px solid rgba(41, 121, 255, 0.3);
		border-radius: 20px;
		animation: pulse-ring 2s infinite;
	}
	
	@keyframes pulse-ring {
		0% {
			transform: translate(-50%, -50%) scale(1);
			opacity: 1;
		}
		100% {
			transform: translate(-50%, -50%) scale(1.5);
			opacity: 0;
		}
	}
}

.tracker-info {
	display: flex;
	align-items: center;
	margin-bottom: 20px;
	
	.tracker-details {
		margin-left: 12px;
		
		.charger-name {
			font-size: 15px;
			font-weight: bold;
			color: #333;
			display: block;
		}
		
		.charger-vehicle {
			font-size: 12px;
			color: #999;
			margin-top: 4px;
		}
	}
}

.progress-container {
	margin-bottom: 20px;
	
	.progress-bar {
					width: 100%;
					height: 8px;
					background: #f0f0f0;
					border-radius: 4px;
					overflow: hidden;
					position: relative;
					
					.progress-fill {
						height: 100%;
						background: linear-gradient(90deg, #2979ff, #0052d9);
						border-radius: 4px;
						transition: width 0.5s ease;
					}
					
					.progress-value {
						position: absolute;
						top: -20px;
						right: 0;
						background: rgba(41, 121, 255, 0.9);
						color: #fff;
						padding: 2px 8px;
						border-radius: 10px;
						font-size: 12px;
						font-weight: bold;
						animation: pulse 1s infinite alternate;
					}
				}
	
	.eta {
					font-size: 13px;
					color: #666;
					margin-top: 8px;
					display: flex;
					align-items: center;
				}
				
				.eta-time {
					color: #2979ff;
					font-weight: bold;
					margin-left: 4px;
					animation: pulse 2s infinite alternate;
				}
}

.map-container {
	
	.map-placeholder {
					width: 100%;
					height: 120px;
					background: rgba(41,121,255,0.05);
					border-radius: 12px;
					display: flex;
					flex-direction: column;
					align-items: center;
					justify-content: center;
					position: relative;
					
					.map-icon-container {
						position: relative;
						display: inline-block;
					}
					
					.map-glow {
						position: absolute;
						top: 50%;
						left: 50%;
						transform: translate(-50%, -50%);
						width: 80px;
						height: 80px;
						background: radial-gradient(circle, rgba(41, 121, 255, 0.3), transparent 70%);
						border-radius: 50%;
						animation: glow-pulse 2s infinite alternate;
					}
					
					.map-text {
						font-size: 14px;
						color: #333;
						margin-top: 8px;
						font-weight: bold;
					}
					
					.map-subtext {
						font-size: 12px;
						color: #666;
						margin-top: 4px;
					}
					
					.map-path {
						display: flex;
						gap: 8px;
						margin-top: 12px;
					}
					
					.path-dot {
						width: 6px;
						height: 6px;
						background: #2979ff;
						border-radius: 50%;
						animation: path-pulse 1.5s infinite;
					}
					
					.path-dot:nth-child(2) {
						animation-delay: 0.5s;
					}
					
					.path-dot:nth-child(3) {
						animation-delay: 1s;
					}
				}
				
				@keyframes glow-pulse {
					0% {
						transform: translate(-50%, -50%) scale(1);
						opacity: 0.5;
					}
					100% {
						transform: translate(-50%, -50%) scale(1.2);
						opacity: 0.8;
					}
				}
				
				@keyframes path-pulse {
					0% {
						transform: scale(1);
						opacity: 0.5;
					}
					50% {
						transform: scale(1.5);
						opacity: 1;
					}
					100% {
						transform: scale(1);
						opacity: 0.5;
					}
				}
}

/* 按钮悬停效果 */
.btn-hover {
	transform: translateY(-4px) scale(1.02);
	box-shadow: 0 10px 30px rgba(41, 121, 255, 0.5);
	transition: all 0.3s ease;
	background: linear-gradient(135deg, #2979ff, #0052d9);
}

/* 按钮点击效果 */
.u-button:active {
	transform: translateY(0) scale(0.98);
	box-shadow: 0 4px 15px rgba(41, 121, 255, 0.3);
	transition: all 0.1s ease;
}

/* 科技感按钮效果 */
.u-button {
	position: relative;
	overflow: hidden;
	
	&::before {
		content: '';
		position: absolute;
		top: -50%;
		left: -50%;
		width: 200%;
		height: 200%;
		background: linear-gradient(
			to bottom right,
			rgba(255, 255, 255, 0) 0%,
			rgba(255, 255, 255, 0.2) 50%,
			rgba(255, 255, 255, 0) 100%
		);
		transform: rotate(45deg);
		animation: shine 3s infinite;
	}
}

/* 卡片悬浮效果增强 */
.banner-card,
.form-card,
.tracker-card,
.ai-analysis-card {
	transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
	
	&:hover {
		transform: translateY(-8px) rotateX(5deg) rotateY(5deg);
		box-shadow: 0 25px 50px rgba(0,0,0,0.2);
	}
}

/* 列表项交互效果 */
.ai-analysis-item {
	transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
	
	&:hover {
		transform: translateX(10px) scale(1.02);
		background: rgba(240, 245, 255, 0.95);
		box-shadow: 0 10px 20px rgba(41, 121, 255, 0.1);
	}
}

/* 路径选项交互效果 */
.path-option {
	transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
	
	&:hover {
		transform: translateY(-4px) scale(1.05);
		background: rgba(255, 255, 255, 0.2);
		border-color: #2979ff;
		box-shadow: 0 8px 16px rgba(41, 121, 255, 0.3);
	}
}

/* 发光效果动画 */
@keyframes shine {
	0% {
		transform: translateX(-100%) rotate(45deg);
	}
	100% {
		transform: translateX(100%) rotate(45deg);
	}
}

/* 科技感加载动画 */
@keyframes tech-loading {
	0% {
		transform: rotate(0deg) scale(1);
	}
	50% {
		transform: rotate(180deg) scale(1.2);
	}
	100% {
		transform: rotate(360deg) scale(1);
	}
}

/* 脉冲效果增强 */
@keyframes pulse {
	0% {
		transform: scale(1);
		box-shadow: 0 0 0 0 rgba(41, 121, 255, 0.7);
	}
	70% {
		transform: scale(1.05);
		box-shadow: 0 0 0 10px rgba(41, 121, 255, 0);
	}
	100% {
		transform: scale(1);
		box-shadow: 0 0 0 0 rgba(41, 121, 255, 0);
	}
}

/* 性能优化 */
.banner-card .flare {
	will-change: transform, opacity;
}

.banner-txt {
	will-change: transform, opacity;
}

.form-card {
	will-change: transform, opacity;
}

.notice {
	will-change: transform, opacity;
}

.u-button {
	will-change: transform, box-shadow;
}

.tracker-card {
	will-change: transform, opacity;
}

.progress-fill {
	will-change: width;
}

/* 增强视觉效果 */
.banner-card {
	transition: transform 0.3s ease, box-shadow 0.3s ease;
	transform-style: preserve-3d;
	
	&:hover {
		transform: translateY(-5px) rotateX(5deg) rotateY(5deg);
		box-shadow: 0 20px 40px rgba(0,0,0,0.15);
	}
}

.form-card {
	transition: all 0.3s ease;
	transform-style: preserve-3d;
	
	&:hover {
		box-shadow: 0 20px 40px rgba(0,0,0,0.15);
		transform: translateY(-5px) rotateX(3deg) rotateY(3deg);
	}
}

.tracker-card {
	transition: all 0.3s ease;
	transform-style: preserve-3d;
	
	&:hover {
		box-shadow: 0 20px 40px rgba(0,0,0,0.15);
		transform: translateY(-5px) rotateX(3deg) rotateY(3deg);
	}
}

.map-placeholder {
	transition: all 0.3s ease;
	
	&:hover {
		background: rgba(41,121,255,0.1);
		transform: scale(1.02);
	}
}

/* 加载动画优化 */
@keyframes pulse {
	0% { transform: scale(1); }
	50% { transform: scale(1.05); }
	100% { transform: scale(1); }
}

@keyframes loading-spin {
	0% { transform: rotate(0deg); }
	100% { transform: rotate(360deg); }
}

@keyframes loading-pulse {
	0% {
		transform: scale(0.8);
		opacity: 0.5;
	}
	50% {
		transform: scale(1.2);
		opacity: 1;
	}
	100% {
		transform: scale(0.8);
		opacity: 0.5;
	}
}

.u-button[loading] {
	animation: loading-pulse 1.5s infinite ease-in-out;
}

/* 页面元素入场动画 */
.banner-card {
	animation: slideInUp 0.8s ease-out;
}

.form-card {
	animation: slideInUp 0.6s ease-out 0.2s both;
}

.notice {
	animation: fadeIn 0.8s ease-out 0.4s both;
}

.footer {
	animation: fadeInUp 1s ease-out 0.6s both;
}

.tracker-card {
	animation: slideInUp 0.8s ease-out;
}

/* 响应式优化 */
@media screen and (max-width: 375px) {
	.tracker-card {
		padding: 15px;
	}
	
	.tracker-info {
		margin-bottom: 15px;
	}
	
	.map-placeholder {
		height: 100px;
	}
	
	.footer-actions {
		gap: 10px;
	}
}

@media screen and (max-width: 320px) {
	.tracker-card {
		padding: 12px;
	}
	
	.charger-name {
		font-size: 14px !important;
	}
	
	.map-placeholder {
		height: 80px;
	}
	
	.map-text {
		font-size: 12px !important;
	}
	
	.map-subtext {
		font-size: 10px !important;
	}
}

/* 动画效果 */
@keyframes breathe {
	from { opacity: 0.3; transform: scale(1); }
	to { opacity: 0.6; transform: scale(1.3); }
}

@keyframes fadeIn {
	from { opacity: 0; transform: translateY(10px); }
	to { opacity: 1; transform: translateY(0); }
}

@keyframes fadeInUp {
	from { opacity: 0; transform: translateY(20px); }
	to { opacity: 1; transform: translateY(0); }
}

@keyframes slideInUp {
	from { opacity: 0; transform: translateY(30px); }
	to { opacity: 1; transform: translateY(0); }
}

/* 响应式设计 */
@media screen and (max-width: 375px) {
	.banner-card {
		height: 160px;
	}
	
	.banner-txt {
		font-size: 18px;
		bottom: 20px;
		left: 20px;
	}
	
	.takeaway-container {
		padding: 15px;
	}
	
	.footer {
		bottom: 30px;
		left: 15px;
		right: 15px;
	}
}

@media screen and (max-width: 320px) {
	.banner-card {
		height: 140px;
	}
	
	.banner-txt {
		font-size: 16px;
		bottom: 15px;
		left: 15px;
	}
	
	.form-card {
		border-radius: 16px;
	}
	
	.notice {
		font-size: 12px;
		padding: 12px;
	}
}

@media screen and (min-width: 768px) {
	.takeaway-container {
		max-width: 600px;
		margin: 0 auto;
		padding: 30px;
	}
	
	.banner-card {
		height: 220px;
	}
	
	.banner-txt {
		font-size: 24px;
		bottom: 30px;
		left: 30px;
	}
}
</style>