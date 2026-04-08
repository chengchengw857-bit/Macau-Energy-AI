<template>
	<view class="route-page">
		<!-- 粒子背景 -->
		<view class="particles-container">
			<view v-for="n in 20" :key="n" class="particle" :class="{'particle-type-1': n % 3 === 0, 'particle-type-2': n % 3 === 1, 'particle-type-3': n % 3 === 2}" :style="{animationDelay: n * 0.1 + 's'}"></view>
		</view>
		<u-navbar title="AI 智慧路径规划" :autoBack="true" placeholder bgColor="#0a1128">
			<view class="ai-badge" slot="right">
				<u-icon name="robot" size="16" color="#2979ff"></u-icon>
				<text>AI智能分析</text>
			</view>
		</u-navbar>
		
		<!-- 目的地输入 -->
		<view class="destination-input">
			<u-icon name="location" size="20" color="#2979ff" class="input-icon"></u-icon>
			<view class="input-content">
				<text class="input-label">目的地</text>
				<text class="input-value">{{destination || '点击输入目的地'}}</text>
			</view>
			<u-icon name="arrow-right" size="20" color="#999" class="input-arrow"></u-icon>
		</view>
		
		<!-- 路径偏好设置 -->
		<view class="preference-settings">
			<view class="preference-header">
				<u-icon name="settings" size="18" color="#2979ff"></u-icon>
				<text class="preference-title">路径偏好设置</text>
			</view>
			<view class="preference-options">
				<view 
					v-for="option in preferences" 
					:key="option.id"
					class="preference-option"
					:class="{active: selectedPreferences.includes(option.id)}"
					@click="togglePreference(option.id)"
				>
					<u-icon :name="option.icon" size="18" :color="selectedPreferences.includes(option.id) ? '#2979ff' : '#666'"></u-icon>
					<text :class="{active: selectedPreferences.includes(option.id)}">{{option.name}}</text>
				</view>
			</view>
		</view>
		
		<!-- 实时状态卡片 -->
		<view class="status-card">
			<view class="status-item">
				<view class="status-icon battery-icon">
					<text class="icon-emoji">🔋</text>
				</view>
				<view class="status-info">
					<text class="status-label">当前电量</text>
					<text class="status-value">22%</text>
				</view>
			</view>
			<view class="status-item">
				<view class="status-icon car-icon">
					<text class="icon-emoji">🚗</text>
				</view>
				<view class="status-info">
					<text class="status-label">预计续航</text>
					<text class="status-value">58km</text>
				</view>
			</view>
			<view class="status-item">
				<view class="status-icon weather-icon">
					<text class="icon-emoji">☀️</text>
				</view>
				<view class="status-info">
					<text class="status-label">天气状况</text>
					<text class="status-value">晴 25°C</text>
				</view>
			</view>
			<view class="status-item">
				<view class="status-icon traffic-icon">
					<text class="icon-emoji">🛣️</text>
				</view>
				<view class="status-info">
					<text class="status-label">交通状况</text>
					<text class="status-value">畅通</text>
				</view>
			</view>
		</view>
		
		<!-- 路径选项卡 -->
		<view class="path-tabs">
			<view 
				v-for="(path, index) in paths" 
				:key="path.id"
				class="path-tab"
				:class="{active: activePath === index}"
				@click="activePath = index"
			>
				<text class="path-tab-title">{{path.name}}</text>
				<text class="path-tab-info">{{path.totalTime}} | {{path.cost}}</text>
			</view>
		</view>
		
		<!-- 路径详情 -->
		<view class="path-detail" v-for="(path, index) in paths" :key="path.id" v-show="activePath === index">
			<!-- 路径概览 -->
			<view class="path-overview">
				<view class="overview-item">
					<u-icon name="map" size="20" color="#2979ff"></u-icon>
					<text class="overview-label">总行程</text>
					<text class="overview-value">{{path.totalDistance}}</text>
				</view>
				<view class="overview-item">
					<u-icon name="clock" size="20" color="#2979ff"></u-icon>
					<text class="overview-label">总时间</text>
					<text class="overview-value">{{path.totalTime}}</text>
				</view>
				<view class="overview-item">
					<u-icon name="money" size="20" color="#2979ff"></u-icon>
					<text class="overview-label">总费用</text>
					<text class="overview-value">{{path.cost}}</text>
				</view>
				<view class="overview-item">
					<u-icon name="battery" size="20" color="#2979ff"></u-icon>
					<text class="overview-label">能耗</text>
					<text class="overview-value">{{path.energyConsumption}}</text>
				</view>
			</view>
			
			<!-- 路径地图 -->
			<view class="path-map">
				<image src="https://cdn.uviewui.com/uview/swiper/swiper2.png" mode="aspectFill" class="map-image"></image>
				<view class="map-overlay">
					<view class="map-point start">
						<text class="map-icon">📍</text>
					</view>
					<view class="map-point middle" v-for="(stop, stopIndex) in path.energyStops" :key="stopIndex">
						<text class="map-icon">🔋</text>
					</view>
					<view class="map-point end">
						<text class="map-icon">🚩</text>
					</view>
				</view>
				<!-- AR导航按钮 -->
				<view class="map-ar-button" @click="openARNavigation">
					<u-icon name="scan" size="20" color="#fff"></u-icon>
					<text>AR导航</text>
				</view>
			</view>
			
			<!-- 实时交通状况 -->
			<view class="traffic-status">
				<view class="traffic-header">
					<u-icon name="traffic" size="16" color="#722ed1"></u-icon>
					<text class="traffic-title">实时交通状况</text>
				</view>
				<view class="traffic-info">
					<view class="traffic-segment" v-for="(segment, index) in trafficSegments" :key="index">
						<view class="segment-info">
							<text class="segment-name">{{segment.name}}</text>
							<text class="segment-status" :class="segment.status">{{segment.statusText}}</text>
						</view>
						<view class="segment-bar">
							<view class="segment-progress" :class="segment.status" :style="{width: segment.progress + '%'}"></view>
						</view>
					</view>
				</view>
			</view>
			
			<!-- 路径步骤 -->
			<view class="path-steps">
				<u-steps current="1" direction="column" activeColor="#2979ff">
					<u-steps-item title="当前位置" desc="剩余电量 22%"></u-steps-item>
					<u-steps-item 
						v-for="(stop, stopIndex) in path.energyStops" 
						:key="stopIndex"
						title="中途补能点"
						:desc="stop.name + ' (预计' + stop.time + '到达)'
					"></u-steps-item>
					<u-steps-item title="目的地" :desc="destination || '横琴口岸'"></u-steps-item>
				</u-steps>
			</view>
			
			<!-- 补能站点详情 -->
			<view class="energy-stops" v-if="path.energyStops.length > 0">
				<text class="section-title">补能站点详情</text>
				<view 
					v-for="(stop, stopIndex) in path.energyStops" 
					:key="stopIndex"
					class="energy-stop-card"
				>
					<view class="stop-header">
						<u-tag :text="stop.type" size="mini" :type="stop.type === '换电站' ? 'error' : 'primary'" class="stop-type"></u-tag>
						<text class="stop-name">{{stop.name}}</text>
						<u-tag text="推荐" size="mini" type="success" class="recommend-tag"></u-tag>
					</view>
					<view class="stop-details">
						<view class="stop-detail-item">
							<u-icon name="map" size="14" color="#666"></u-icon>
							<text>距离：{{stop.distance}}</text>
						</view>
						<view class="stop-detail-item">
							<u-icon name="clock" size="14" color="#666"></u-icon>
							<text>预计到达：{{stop.time}}</text>
						</view>
						<view class="stop-detail-item">
							<u-icon name="time" size="14" color="#666"></u-icon>
							<text>补能时间：{{stop.chargingTime}}</text>
						</view>
						<view class="stop-detail-item">
							<u-icon name="money" size="14" color="#666"></u-icon>
							<text>费用：{{stop.price}}</text>
						</view>
						<view class="stop-detail-item">
							<u-icon name="star" size="14" color="#666"></u-icon>
							<text>评分：{{stop.rating}}</text>
						</view>
					</view>
					<view class="stop-actions">
						<u-button type="primary" text="预约" size="small" @click="reserveStop(stop)"></u-button>
						<u-button type="default" text="导航" size="small" @click="navigateToStop(stop)"></u-button>
						<u-button type="default" text="详情" size="small" @click="viewStopDetail(stop)"></u-button>
					</view>
				</view>
			</view>
			
			<!-- AI分析结果 -->
			<view class="ai-analysis">
				<view class="ai-analysis-header">
					<u-icon name="scan" size="16" color="#2979ff"></u-icon>
					<text class="ai-analysis-title">AI智能分析</text>
				</view>
				<text class="ai-analysis-content">{{path.description}}</text>
				<view class="ai-analysis-tags">
					<u-tag text="实时交通数据" size="mini" type="info" class="ai-tag"></u-tag>
					<u-tag text="车辆能耗分析" size="mini" type="info" class="ai-tag"></u-tag>
					<u-tag text="充电站点优化" size="mini" type="info" class="ai-tag"></u-tag>
				</view>
			</view>
			
			<!-- 路线建议 -->
			<view class="route-tips">
				<view class="tips-header">
					<u-icon name="info-circle" size="16" color="#2979ff"></u-icon>
					<text class="tips-title">路线建议</text>
				</view>
				<text class="tips-content">根据当前交通状况，建议提前10分钟出发，避开高峰期拥堵。补能站点当前排队较少，可直接前往。</text>
			</view>
			
			<!-- 天气详情 -->
			<view class="weather-detail">
				<view class="weather-header">
					<u-icon name="weather-sunny" size="16" color="#faad14"></u-icon>
					<text class="weather-title">天气详情</text>
				</view>
				<view class="weather-info">
					<view class="weather-item">
						<text class="weather-label">温度</text>
						<text class="weather-value">25°C</text>
					</view>
					<view class="weather-item">
						<text class="weather-label">湿度</text>
						<text class="weather-value">45%</text>
					</view>
					<view class="weather-item">
						<text class="weather-label">风速</text>
						<text class="weather-value">3级</text>
					</view>
					<view class="weather-item">
						<text class="weather-label">降水</text>
						<text class="weather-value">0%</text>
					</view>
				</view>
			</view>
		</view>
		
		<!-- 底部操作按钮 -->
		<view class="bottom-actions">
			<u-button type="primary" text="开始导航" shape="circle" @click="startNavigation" class="primary-button"></u-button>
			<u-button type="default" text="分享路径" shape="circle" @click="sharePath" class="secondary-button"></u-button>
			<u-button type="default" text="保存路径" shape="circle" @click="savePath" class="secondary-button"></u-button>
		</view>
		
		<!-- 快捷功能栏 -->
		<view class="quick-actions">
			<view class="quick-action-item" @click="openAIMap">
				<u-icon name="map" size="24" color="#2979ff"></u-icon>
				<text>AI智能地图</text>
			</view>
			<view class="quick-action-item" @click="openOneStop">
				<u-icon name="calendar" size="24" color="#52c41a"></u-icon>
				<text>一站式预约</text>
			</view>
			<view class="quick-action-item" @click="openARNav">
				<u-icon name="scan" size="24" color="#faad14"></u-icon>
				<text>AR实景导航</text>
			</view>
			<view class="quick-action-item" @click="openCommunity">
				<u-icon name="chat" size="24" color="#722ed1"></u-icon>
				<text>互动社区</text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			activePath: 0,
			destination: '',
			paths: [
			{
				id: 'fastest',
				name: '最快到达',
				totalDistance: '32.5km',
				totalTime: '35分钟',
				cost: '¥25',
				energyConsumption: '8.2kWh',
				description: '基于实时交通数据和充电排队预测模型，为您规划最短时间路径。虽然距离稍长，但可节省约10分钟时间。',
				energyStops: [
					{
						name: '澳门半岛换电站',
						type: '换电站',
						distance: '15.2km',
						time: '15:30',
						chargingTime: '5分钟',
						price: '¥20',
						rating: '4.8'
					}
				]
			},
			{
				id: 'least',
				name: '最少补能',
				totalDistance: '28.3km',
				totalTime: '40分钟',
				cost: '¥30',
				energyConsumption: '7.5kWh',
				description: '根据您的驾驶习惯计算动态续航，规划能量最小消耗路径。仅需一次补能，可减少途中停留时间。',
				energyStops: [
					{
						name: '氹仔岛充电站',
						type: '充电站',
						distance: '12.5km',
						time: '15:45',
						chargingTime: '20分钟',
						price: '¥30',
						rating: '4.6'
					}
				]
			},
			{
				id: 'cheapest',
				name: '最低成本',
				totalDistance: '35.7km',
				totalTime: '45分钟',
				cost: '¥18',
				energyConsumption: '9.0kWh',
				description: '实时解析澳门电力CEM的动态电价，推荐电价低谷时段补能。虽然距离较长，但可节省约7元费用。',
				energyStops: [
					{
						name: '路环充电站',
						type: '充电站',
						distance: '18.3km',
						time: '16:00',
						chargingTime: '25分钟',
						price: '¥18',
						rating: '4.5'
					}
				]
			}
		],
		trafficSegments: [
			{ name: '澳门半岛路段', status: 'smooth', statusText: '畅通', progress: 20 },
			{ name: '友谊大桥', status: 'slow', statusText: '缓行', progress: 60 },
			{ name: '氹仔岛路段', status: 'smooth', statusText: '畅通', progress: 30 }
		],
			preferences: [
			{ id: 'avoidHighway', name: '避开高速', icon: 'car' },
			{ id: 'avoidTolls', name: '避开收费站', icon: 'money' },
			{ id: 'ecoFriendly', name: '环保模式', icon: 'leaf' },
			{ id: 'scenicRoute', name: '风景路线', icon: 'image' }
		],
			selectedPreferences: []
		}
	},
	onLoad(options) {
		if (options.pathData) {
			const pathData = JSON.parse(decodeURIComponent(options.pathData));
			this.destination = pathData.destination;
			this.paths = pathData.paths;
		}
	},
	methods: {
		// 目的地输入
		inputDestination() {
			// 这里可以打开地址选择器
			uni.showToast({
				title: '打开目的地选择',
				icon: 'none',
				duration: 1000
			});
		},
		// 路径偏好设置
		togglePreference(preferenceId) {
			const index = this.selectedPreferences.indexOf(preferenceId);
			if (index > -1) {
				this.selectedPreferences.splice(index, 1);
			} else {
				this.selectedPreferences.push(preferenceId);
			}
			// 这里可以根据偏好重新计算路径
			this.calculatePaths();
		},
		// 计算路径
		calculatePaths() {
			// 模拟路径计算
			uni.showToast({
				title: '路径已更新',
				icon: 'success',
				duration: 1000
			});
		},
		// 预约站点
		reserveStop(stop) {
			// 跳转到预约页面，并传递站点信息
			uni.navigateTo({
				url: `/pages/reserve/reserve?stop=${encodeURIComponent(JSON.stringify(stop))}`
			});
		},
		// 导航到站点
		navigateToStop(stop) {
			uni.showToast({
				title: `正在导航到${stop.name}`,
				icon: 'none',
				duration: 1000
			});
		},
		// 查看站点详情
		viewStopDetail(stop) {
			// 跳转到站点详情页面
			uni.showToast({
				title: `查看${stop.name}详情`,
				icon: 'none',
				duration: 1000
			});
		},
		// 开始导航
		startNavigation() {
			uni.showToast({
				title: '开始导航',
				icon: 'success',
				duration: 1000
			});
		},
		// 分享路径
		sharePath() {
			uni.showToast({
				title: '分享路径',
				icon: 'none',
				duration: 1000
			});
		},
		// 保存路径
		savePath() {
			uni.showToast({
				title: '路径已保存',
				icon: 'success',
				duration: 1000
			});
		},
		// 获取徽章文本
		getBadgeText(pathId) {
			const badges = {
				fastest: '最快',
				least: '最少补能',
				cheapest: '最省钱'
			};
			return badges[pathId] || '';
		},
		// 打开AI智能地图
		openAIMap() {
			uni.navigateTo({ url: '/pages/map/map' });
		},
		// 打开一站式预约
		openOneStop() {
			uni.navigateTo({ url: '/pages/reserve/reserve' });
		},
		// 打开AR实景导航
		openARNav() {
			uni.navigateTo({ url: '/pages/ar/ar' });
		},
		// 打开AR导航
		openARNavigation() {
			uni.navigateTo({ url: '/pages/ar/ar' });
		},
		// 打开互动社区
		openCommunity() {
			uni.showToast({
				title: '打开互动社区',
				icon: 'none',
				duration: 1000
			});
		}
	}
}
</script>

<style lang="scss" scoped>
.route-page {
	background: linear-gradient(135deg, #f8faff 0%, #e6f0ff 100%);
	min-height: 100vh;
	padding-bottom: 100px;
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

/* AI徽章 */
.ai-badge {
	display: flex;
	align-items: center;
	gap: 4px;
	background: rgba(41, 121, 255, 0.1);
	padding: 6px 10px;
	border-radius: 16px;
	border: 1px solid rgba(41, 121, 255, 0.3);
	box-shadow: 0 2px 8px rgba(41, 121, 255, 0.15);
	
	text {
		font-size: 12px;
		color: #2979ff;
		font-weight: bold;
	}
}

/* 目的地输入 */
.destination-input {
	display: flex;
	align-items: center;
	padding: 16px 20px;
	background: rgba(255, 255, 255, 0.95);
	border-radius: 16px;
	margin: 16px 20px;
	box-shadow: 0 4px 15px rgba(0,0,0,0.05);
	backdrop-filter: blur(10px);
	border: 1px solid rgba(255, 255, 255, 0.3);
	cursor: pointer;
	transition: all 0.3s ease;
	
	&:hover {
		transform: translateY(-2px);
		box-shadow: 0 6px 20px rgba(0,0,0,0.1);
	}
	
	.input-icon {
		margin-right: 12px;
	}
	
	.input-content {
		flex: 1;
		
		.input-label {
			font-size: 12px;
			color: #888;
			display: block;
			margin-bottom: 4px;
		}
		
		.input-value {
			font-size: 16px;
			color: #333;
			font-weight: 500;
		}
	}
	
	.input-arrow {
		margin-left: 12px;
	}
}

/* 路径偏好设置 */
.preference-settings {
	background: rgba(255, 255, 255, 0.95);
	border-radius: 16px;
	margin: 0 20px 20px;
	box-shadow: 0 4px 15px rgba(0,0,0,0.05);
	backdrop-filter: blur(10px);
	border: 1px solid rgba(255, 255, 255, 0.3);
	padding: 16px;
	
	.preference-header {
		display: flex;
		align-items: center;
		gap: 8px;
		margin-bottom: 16px;
		
		.preference-title {
			font-size: 15px;
			font-weight: 600;
			color: #333;
		}
	}
	
	.preference-options {
		display: flex;
		flex-wrap: wrap;
		gap: 12px;
		
		.preference-option {
			display: flex;
			align-items: center;
			gap: 8px;
			padding: 10px 16px;
			border-radius: 20px;
			background: #f8f9fb;
			border: 1px solid #e8e8e8;
			cursor: pointer;
			transition: all 0.3s ease;
			
			&:hover {
				transform: translateY(-2px);
				box-shadow: 0 4px 12px rgba(0,0,0,0.08);
			}
			
			&.active {
				background: rgba(41, 121, 255, 0.1);
				border-color: #2979ff;
				
				text.active {
					color: #2979ff;
					font-weight: 600;
				}
			}
			
			text {
				font-size: 13px;
				color: #666;
				transition: all 0.3s ease;
			}
		}
	}
}

/* 实时状态卡片 */
.status-card {
	display: flex;
	justify-content: space-around;
	padding: 20px;
	background: rgba(255, 255, 255, 0.95);
	border-radius: 20px;
	margin: 0 20px 24px;
	box-shadow: 0 6px 20px rgba(0,0,0,0.06);
	backdrop-filter: blur(10px);
	border: 1px solid rgba(255, 255, 255, 0.3);
	
	.status-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 12px;
		padding: 20px 15px;
		border-radius: 16px;
		transition: all 0.3s ease;
		background: rgba(255, 255, 255, 0.8);
		backdrop-filter: blur(10px);
		border: 1px solid rgba(255, 255, 255, 0.3);
		
		&:hover {
			background: rgba(255, 255, 255, 0.9);
			transform: translateY(-3px);
			box-shadow: 0 8px 25px rgba(0,0,0,0.1);
		}
		
		.status-icon {
			display: flex;
			align-items: center;
			justify-content: center;
			width: 56px;
			height: 56px;
			border-radius: 16px;
			margin-bottom: 8px;
			
			&.battery-icon {
				background: linear-gradient(135deg, rgba(82, 196, 26, 0.1), rgba(82, 196, 26, 0.05));
			}
			
			&.car-icon {
				background: linear-gradient(135deg, rgba(41, 121, 255, 0.1), rgba(41, 121, 255, 0.05));
			}
			
			&.weather-icon {
				background: linear-gradient(135deg, rgba(250, 173, 20, 0.1), rgba(250, 173, 20, 0.05));
			}
			
			&.traffic-icon {
				background: linear-gradient(135deg, rgba(114, 46, 209, 0.1), rgba(114, 46, 209, 0.05));
			}
		}
		
		.icon-emoji {
			font-size: 32px;
			line-height: 1;
		}
		
		.status-info {
			display: flex;
			flex-direction: column;
			align-items: center;
			padding: 0 10px;
			
			.status-label {
				font-size: 12px;
				color: #666;
				font-weight: 600;
				margin-bottom: 4px;
				text-align: center;
			}
			
			.status-value {
				font-size: 18px;
				font-weight: 700;
				color: #1a1a1a;
				text-align: center;
			}
		}
	}
}

/* 路径选项卡 */
.path-tabs {
	display: flex;
	padding: 0 20px;
	gap: 12px;
	margin-bottom: 24px;

	.path-tab {
		flex: 1;
		padding: 24px 15px;
		background: rgba(255, 255, 255, 0.9);
		border-radius: 16px;
		box-shadow: 0 4px 15px rgba(0,0,0,0.05);
		border: 1px solid rgba(255, 255, 255, 0.3);
		backdrop-filter: blur(10px);
		transition: all 0.3s ease;
		cursor: pointer;
		text-align: center;
		position: relative;
		overflow: hidden;
		
		&:hover {
			transform: translateY(-3px);
			box-shadow: 0 8px 25px rgba(0,0,0,0.1);
		}
		
		&.active {
			background: linear-gradient(135deg, #2979ff, #0052d9);
			border-color: #2979ff;
			box-shadow: 0 8px 25px rgba(41, 121, 255, 0.4);
			
			.path-tab-title {
				color: #fff;
			}
			
			.path-tab-info {
				color: rgba(255, 255, 255, 0.9);
			}
			
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
		
		.path-tab-title {
			font-size: 18px;
			font-weight: 600;
			color: #1a1a1a;
			display: block;
			margin-bottom: 8px;
			position: relative;
			z-index: 1;
		}
		
		.path-tab-info {
			font-size: 14px;
			color: #666;
			display: block;
			font-weight: 500;
			position: relative;
			z-index: 1;
		}
	}
}

/* 路径地图 */
.path-map {
	position: relative;
	margin-bottom: 24px;
	border-radius: 16px;
	overflow: hidden;
	box-shadow: 0 6px 20px rgba(0,0,0,0.1);

	.map-image {
		width: 100%;
		height: 280px;
		border-radius: 16px;
	}
	
	.map-overlay {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: space-around;
		
		.map-point {
			display: flex;
			align-items: center;
			justify-content: center;
			width: 44px;
			height: 44px;
			background: rgba(255, 255, 255, 0.95);
			border-radius: 50%;
			box-shadow: 0 4px 12px rgba(0,0,0,0.25);
			transition: all 0.3s ease;
			
			&:hover {
				transform: scale(1.1);
				box-shadow: 0 6px 16px rgba(0,0,0,0.3);
			}
			
			&.start {
				background: rgba(82, 196, 26, 0.95);
				color: #fff;
			}
			
			&.middle {
				background: rgba(41, 121, 255, 0.95);
				color: #fff;
			}
			
			&.end {
				background: rgba(250, 173, 20, 0.95);
				color: #fff;
			}
		}
		
		.map-icon {
			font-size: 24px;
			line-height: 1;
		}
	}
	
	/* AR导航按钮 */
	.map-ar-button {
		position: absolute;
		bottom: 16px;
		right: 16px;
		display: flex;
		align-items: center;
		gap: 8px;
		padding: 12px 16px;
		background: linear-gradient(135deg, #faad14, #ff9a00);
		border-radius: 24px;
		box-shadow: 0 4px 15px rgba(250, 173, 20, 0.4);
		color: #fff;
		font-size: 14px;
		font-weight: 600;
		cursor: pointer;
		transition: all 0.3s ease;
		
		&:hover {
			transform: translateY(-3px);
			box-shadow: 0 6px 20px rgba(250, 173, 20, 0.5);
		}
		
		text {
			font-size: 14px;
			font-weight: 600;
		}
	}
}

/* 路径详情 */
.path-detail {
	background: #fff;
	margin: 0 20px;
	border-radius: 24px;
	box-shadow: 0 10px 30px rgba(0,0,0,0.05);
	padding: 24px;
	
	/* 路径概览 */
	.path-overview {
		display: flex;
		justify-content: space-around;
		margin-bottom: 24px;
		padding: 20px;
		background: #f8f9fb;
		border-radius: 16px;
		
		.overview-item {
			display: flex;
			flex-direction: column;
			align-items: center;
			gap: 6px;
			padding: 0 10px;
			
			.overview-label {
				font-size: 12px;
				color: #666;
				font-weight: 600;
				text-align: center;
			}
			
			.overview-value {
				font-size: 16px;
				font-weight: 700;
				color: #1a1a1a;
				text-align: center;
			}
		}
	}
	
	/* 路径步骤 */
	.path-steps {
		margin-bottom: 24px;
	}
	
	/* 补能站点详情 */
	.energy-stops {
		margin-bottom: 24px;
		
		.section-title {
			font-size: 17px;
			font-weight: 600;
			color: #1a1a1a;
			display: block;
			margin-bottom: 16px;
			letter-spacing: 0.5px;
		}
		
		.energy-stop-card {
			background: #f8f9fb;
			border-radius: 16px;
			padding: 18px;
			margin-bottom: 12px;
			transition: all 0.3s ease;
			
			&:hover {
				transform: translateY(-2px);
				box-shadow: 0 4px 15px rgba(0,0,0,0.05);
			}
			
			.stop-header {
				display: flex;
				align-items: center;
				margin-bottom: 12px;
				
				.stop-type {
					margin-right: 10px;
				}
				
				.stop-name {
					font-size: 15px;
					font-weight: 600;
					color: #1a1a1a;
				}
			}
			
			.stop-details {
				display: flex;
				flex-direction: column;
				gap: 10px;
				margin-bottom: 18px;
				
				.stop-detail-item {
					display: flex;
					align-items: center;
					gap: 10px;
					font-size: 13px;
					color: #666;
					font-weight: 500;
				}
			}
			
			.stop-actions {
				display: flex;
				gap: 12px;
				
				.u-button {
					flex: 1;
					border-radius: 10px;
				}
			}
		}
	}
	
	/* AI分析结果 */
	.ai-analysis {
		background: linear-gradient(135deg, rgba(41, 121, 255, 0.05), rgba(41, 121, 255, 0.02));
		border-radius: 16px;
		padding: 20px;
		border: 1px solid rgba(41, 121, 255, 0.1);
		
		.ai-analysis-header {
			display: flex;
			align-items: center;
			gap: 10px;
			margin-bottom: 12px;
			
			.ai-analysis-title {
				font-size: 15px;
				font-weight: 600;
				color: #2979ff;
			}
		}
		
		.ai-analysis-content {
			font-size: 13px;
			color: #666;
			line-height: 1.5;
			margin-bottom: 12px;
		}
	}
}

/* 实时交通状况 */
.traffic-status {
	background: linear-gradient(135deg, rgba(114, 46, 209, 0.05), rgba(114, 46, 209, 0.02));
	border-radius: 16px;
	padding: 20px;
	margin-top: 24px;
	border: 1px solid rgba(114, 46, 209, 0.1);
	
	.traffic-header {
		display: flex;
		align-items: center;
		gap: 10px;
		margin-bottom: 16px;
		
		.traffic-title {
			font-size: 15px;
			font-weight: 600;
			color: #722ed1;
		}
	}
	
	.traffic-info {
		.traffic-segment {
			margin-bottom: 16px;
			
			&:last-child {
				margin-bottom: 0;
			}
			
			.segment-info {
				display: flex;
				justify-content: space-between;
				align-items: center;
				margin-bottom: 8px;
				
				.segment-name {
					font-size: 13px;
					color: #333;
					font-weight: 500;
				}
				
				.segment-status {
					font-size: 12px;
					font-weight: 600;
					padding: 2px 8px;
					border-radius: 10px;
					
					&.smooth {
						background: rgba(82, 196, 26, 0.1);
						color: #52c41a;
					}
					
					&.slow {
						background: rgba(250, 173, 20, 0.1);
						color: #faad14;
					}
					
					&.congested {
						background: rgba(245, 34, 45, 0.1);
						color: #f5222d;
					}
				}
			}
			
			.segment-bar {
				height: 6px;
				background: #f0f0f0;
				border-radius: 3px;
				overflow: hidden;
				
				.segment-progress {
					height: 100%;
					border-radius: 3px;
					transition: width 0.5s ease;
					
					&.smooth {
						background: #52c41a;
					}
					
					&.slow {
						background: #faad14;
					}
					
					&.congested {
						background: #f5222d;
					}
				}
			}
		}
	}
}

/* 路线建议 */
.route-tips {
	background: linear-gradient(135deg, rgba(41, 121, 255, 0.05), rgba(41, 121, 255, 0.02));
	border-radius: 16px;
	padding: 20px;
	margin-top: 24px;
	border: 1px solid rgba(41, 121, 255, 0.1);
	
	.tips-header {
		display: flex;
		align-items: center;
		gap: 10px;
		margin-bottom: 12px;
		
		.tips-title {
			font-size: 15px;
			font-weight: 600;
			color: #2979ff;
		}
	}
	
	.tips-content {
		font-size: 13px;
		color: #666;
		line-height: 1.5;
		font-weight: 500;
	}
}

/* 天气详情 */
.weather-detail {
	background: linear-gradient(135deg, rgba(250, 173, 20, 0.05), rgba(250, 173, 20, 0.02));
	border-radius: 16px;
	padding: 20px;
	margin-top: 24px;
	border: 1px solid rgba(250, 173, 20, 0.1);
	
	.weather-header {
		display: flex;
		align-items: center;
		gap: 10px;
		margin-bottom: 16px;
		
		.weather-title {
			font-size: 15px;
			font-weight: 600;
			color: #faad14;
		}
	}
	
	.weather-info {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 16px;
		
		.weather-item {
			display: flex;
			justify-content: space-between;
			align-items: center;
			
			.weather-label {
				font-size: 13px;
				color: #888;
				font-weight: 500;
			}
			
			.weather-value {
				font-size: 15px;
				color: #333;
				font-weight: 600;
			}
		}
	}
}

/* 底部操作按钮 */
.bottom-actions {
	position: fixed;
	bottom: 140px;
	left: 20px;
	right: 20px;
	display: flex;
	gap: 12px;
	
	.primary-button {
		flex: 2;
		height: 56px;
		border-radius: 28px;
		background: linear-gradient(135deg, #2979ff, #0052d9);
		box-shadow: 0 6px 20px rgba(41, 121, 255, 0.4);
		font-size: 16px;
		font-weight: 600;
		transition: all 0.3s ease;
		
		&:hover {
			transform: translateY(-2px);
			box-shadow: 0 8px 25px rgba(41, 121, 255, 0.5);
		}
	}
	
	.secondary-button {
		flex: 1;
		height: 56px;
		border-radius: 28px;
		background: #fff;
		border: 1px solid #e0e0e0;
		box-shadow: 0 4px 15px rgba(0,0,0,0.05);
		font-size: 14px;
		font-weight: 500;
		transition: all 0.3s ease;
		
		&:hover {
			transform: translateY(-2px);
			box-shadow: 0 6px 20px rgba(0,0,0,0.1);
		}
	}
}

/* 快捷功能栏 */
.quick-actions {
	position: fixed;
	bottom: 0;
	left: 0;
	right: 0;
	display: flex;
	justify-content: space-around;
	align-items: center;
	padding: 18px 20px;
	background: rgba(255, 255, 255, 0.98);
	backdrop-filter: blur(12px);
	border-top: 1px solid rgba(0,0,0,0.1);
	box-shadow: 0 -6px 20px rgba(0,0,0,0.05);
	
	.quick-action-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 6px;
		cursor: pointer;
		transition: all 0.3s ease;
		padding: 12px;
		border-radius: 16px;
		
		&:hover {
			background: rgba(41, 121, 255, 0.1);
			transform: translateY(-3px);
		}
		
		text {
			font-size: 12px;
			color: #666;
			font-weight: 600;
		}
	}
}
</style>