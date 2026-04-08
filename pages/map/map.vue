<template>
	<view class="map-wrap">
		<!-- 1. 顶部悬浮AI筛选栏 (文档1-②: 智能筛选) -->
		<view class="filter-header">
			<scroll-view scroll-x class="filter-scroll" :show-scrollbar="false">
				<view class="filter-item" :class="{active: activeTab === 0}" @click="activeTab = 0">全部</view>
				<view class="filter-item" :class="{active: activeTab === 1}" @click="activeTab = 1">换电站</view>
				<view class="filter-item" :class="{active: activeTab === 2}" @click="activeTab = 2">≥120kW快充</view>
				<view class="filter-item" :class="{active: activeTab === 3}" @click="activeTab = 3">低价优先</view>
				<view class="filter-item" :class="{active: activeTab === 4}" @click="activeTab = 4">V2G试点</view>
			</scroll-view>
		</view>

		<!-- 2. 地图区域 (文档1-①: GIS地图技术) -->
		<map class="main-map" 
			:latitude="latitude" 
			:longitude="longitude" 
			:markers="markers" 
			:scale="13"
			@markertap="onMarkerTap"
			show-location>
		</map>

		<!-- 3. 底部交互区域：大厂半屏设计 -->
		<view class="interaction-area" :class="{expand: isSelected}">
			<!-- 场景A：附近站点列表 -->
			<scroll-view v-if="!isSelected" scroll-y class="list-container">
				<view class="list-header">
					<text class="list-title">附近推荐站点</text>
					<text class="ai-tips">AI已为您锁定最优路径</text>
				</view>
				<view class="station-card-mini" v-for="(item, index) in stationList" :key="index" @click="selectStation(item)">
					<view class="c-left">
						<view class="name-row">
							<u-tag :text="item.type" size="mini" :type="item.type==='换电站'?'error':'primary'" class="type-tag"></u-tag>
							<text class="s-name">{{item.name}}</text>
						</view>
						<view class="s-tags">
							<text class="t-green">空闲 {{item.idle}}</text>
							<text class="t-blue">{{item.power}}kW</text>
							<text class="t-gray">MOP {{item.price}}/度</text>
						</view>
					</view>
					<view class="c-right">
						<text class="dist">{{item.distance}}km</text>
						<u-icon name="arrow-right" size="14" color="#ccc"></u-icon>
					</view>
				</view>
			</scroll-view>

			<!-- 场景B：站点深度详情 (文档1-①,③: 详细状况与动态显示) -->
			<view v-if="isSelected" class="detail-panel">
				<view class="panel-header">
					<view class="handle-bar" @click="isSelected = false"></view>
					<view class="p-title-row">
						<view class="p-left">
							<text class="p-name">{{currentStation.name}}</text>
							<text class="p-operator">{{currentStation.operator}}</text>
						</view>
						<u-icon name="close-circle-fill" size="24" color="#eee" @click="isSelected = false"></u-icon>
					</view>
				</view>
				
				<view class="p-body">
					<!-- AI 动态排队预测 (文档1-③) -->
					<view class="ai-status-box">
						<u-icon name="clock" color="#ff9900" size="16"></u-icon>
						<text class="ai-wait">预计排队: <text class="bold">{{currentStation.waitTime}}分钟</text></text>
						<text class="ai-trend">| 处于“{{currentStation.busyTrend}}”</text>
					</view>

					<!-- 枪口详细数据 (文档1-①) -->
					<view class="connector-group">
						<view class="conn-item" v-for="(conn, cIdx) in currentStation.connectors" :key="cIdx">
							<view class="conn-type">{{conn.type}}</view>
							<view class="conn-data">
								<text class="pwr">{{conn.power}}kW</text>
								<text class="num">{{conn.idle}}/{{conn.total}} 闲</text>
							</view>
						</view>
					</view>

					<!-- 周边200米配套 (文档1-①) -->
					<view class="amenities-box">
						<text class="a-label">周边 200m 配套</text>
						<view class="a-tags">
							<view class="a-tag" v-for="(ame, aIdx) in currentStation.amenities" :key="aIdx">
								<u-icon :name="ame.icon" size="12"></u-icon>
								<text>{{ame.name}}</text>
							</view>
						</view>
					</view>
					
					<!-- 功能按钮：预约与导航 -->
					<view class="action-buttons">
						<view class="btn-reserve" @click="toReserve">
							<u-icon name="calendar" size="18"></u-icon>
							<text>智能预约</text>
						</view>
						<view class="btn-nav" @click="doNavigation">
							<u-icon name="map-fill" color="#fff" size="18"></u-icon>
							<text>一键导航</text>
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			latitude: 22.189,
			longitude: 113.544,
			activeTab: 0,
			isSelected: false,
			currentStation: {},
			// 模拟全澳聚合数据 (文档1-①核心数据结构)
			stationList: [
				{ 
					id: 101, type: '快充站', name: '乐群楼停车场充电站', operator: '澳门电力 CEM',
					address: '路环石排湾乐群楼地下', power: 120, price: 1.25, idle: 6, distance: 0.8, 
					lat: 22.191, lng: 113.541, waitTime: 5, busyTrend: '平稳时段',
					connectors: [{type:'CCS2', power:120, idle:4, total:6}, {type:'GB/T', power:60, idle:2, total:4}],
					amenities: [{name:'新八佰伴', icon:'shopping-cart'}, {name:'停车场入口', icon:'map'}]
				},
				{ 
					id: 102, type: '换电站', name: '居雅大厦换电一体站', operator: '蔚来能源 NIO',
					address: '路环石排湾居雅大厦南面', power: 300, price: 1.80, idle: 3, distance: 1.2, 
					lat: 22.182, lng: 113.552, waitTime: 0, busyTrend: '低峰时段',
					connectors: [{type:'换电平台', power:300, idle:3, total:12}],
					amenities: [{name:'星巴克', icon:'coffee'}, {name:'餐馆', icon:'home'}]
				},
				{ 
					id: 103, type: '快充站', name: '澳门广场超级充电站', operator: '银河EV',
					address: '澳门半岛罗保博士街2号', power: 180, price: 1.45, idle: 1, distance: 4.5, 
					lat: 22.193, lng: 113.543, waitTime: 15, busyTrend: '高峰时段',
					connectors: [{type:'CCS2', power:180, idle:1, total:8}],
					amenities: [{name:'澳门广场', icon:'shopping-cart'}, {name:'便利店', icon:'bag'}]
				}
			],
			markers: []
		}
	},
	onLoad() {
		this.initMarkers();
	},
	methods: {
		initMarkers() {
			this.markers = this.stationList.map(item => {
				return {
					id: item.id,
					latitude: item.lat,
					longitude: item.lng,
					title: item.name,
					// 文档1-①: 不同图标区分
					iconPath: item.type === '换电站' ? '/static/uview/example/template.png' : '/static/uview/example/js.png',
					width: 32,
					height: 32,
					callout: { 
						content: `${item.name}\n闲 ${item.idle}`, 
						padding: 8, borderRadius: 8, display: 'ALWAYS', fontSize: 10, textAlign: 'center' 
					}
				}
			})
		},
		onMarkerTap(e) {
			const station = this.stationList.find(s => s.id === e.detail.markerId);
			this.selectStation(station);
		},
		selectStation(station) {
			this.currentStation = station;
			this.latitude = station.lat;
			this.longitude = station.lng;
			this.isSelected = true;
		},
		doNavigation() {
			uni.openLocation({
				latitude: this.currentStation.lat,
				longitude: this.currentStation.lng,
				name: this.currentStation.name,
				address: this.currentStation.address
			});
		},
		toReserve() {
			uni.navigateTo({ url: '/pages/reserve/reserve' });
		}
	}
}
</script>

<style lang="scss" scoped>
.map-wrap { display: flex; flex-direction: column; height: 100vh; background: #f7f8fa; position: relative; }

/* 筛选器：文档1-② */
.filter-header {
	position: absolute; top: 15px; left: 0; right: 0; z-index: 100;
	.filter-scroll { white-space: nowrap; width: 100%; padding: 0 15px; box-sizing: border-box; }
	.filter-item {
		display: inline-block; padding: 8px 18px; background: #fff; border-radius: 25px;
		margin-right: 12px; font-size: 13px; font-weight: bold; color: #333;
		box-shadow: 0 4px 15px rgba(0,0,0,0.1);
		&.active { background: #0052D9; color: #fff; }
	}
}

.main-map { width: 100%; flex: 1; }

/* 底部交互区：大厂半屏 */
.interaction-area {
	background: #fff; border-radius: 24px 24px 0 0; margin-top: -24px;
	position: relative; z-index: 10; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
	min-height: 320px; max-height: 50vh; box-shadow: 0 -5px 20px rgba(0,0,0,0.05);
	&.expand { min-height: 420px; }
}

.list-container {
	padding: 20px; box-sizing: border-box;
	.list-header { 
		display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;
		.list-title { font-size: 17px; font-weight: 900; color: #1a1a1a; }
		.ai-tips { font-size: 11px; color: #0052D9; background: #eef3ff; padding: 2px 8px; border-radius: 4px; }
	}
}

.station-card-mini {
	display: flex; justify-content: space-between; align-items: center;
	padding: 18px 0; border-bottom: 1px solid #f0f0f0;
	.name-row { display: flex; align-items: center; margin-bottom: 6px; }
	.type-tag { margin-right: 8px; }
	.s-name { font-size: 15px; font-weight: bold; color: #333; }
	.s-tags {
		display: flex; font-size: 11px;
		.t-green { color: #52c41a; background: #f6ffed; padding: 2px 6px; border-radius: 4px; margin-right: 8px; }
		.t-blue { color: #0052D9; background: #eef3ff; padding: 2px 6px; border-radius: 4px; margin-right: 8px; }
		.t-gray { color: #999; }
	}
	.dist { font-size: 16px; font-weight: bold; color: #0052D9; margin-right: 12px; }
}

/* 详情面板：文档1-①,③核心视觉 */
.detail-panel {
	padding: 0 20px 30px;
	.handle-bar { width: 36px; height: 5px; background: #e0e0e0; border-radius: 3px; margin: 12px auto 20px; }
	.p-title-row { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 15px; }
	.p-name { font-size: 22px; font-weight: 900; color: #1a1a1a; display: block; }
	.p-operator { font-size: 12px; color: #0052D9; font-weight: bold; margin-top: 4px; display: block; }
	
	.ai-status-box {
		background: #fff9f0; padding: 12px 15px; border-radius: 10px; display: flex; align-items: center; margin-bottom: 20px;
		.ai-wait { font-size: 13px; color: #ff9900; margin-left: 8px; .bold { font-weight: 900; font-size: 15px; } }
		.ai-trend { font-size: 13px; color: #999; margin-left: 10px; }
	}

	.connector-group {
		display: flex; gap: 12px; margin-bottom: 20px;
		.conn-item {
			flex: 1; background: #f8f9fb; padding: 12px; border-radius: 12px; border: 1px solid #edf0f5;
			.conn-type { font-size: 11px; color: #999; margin-bottom: 5px; }
			.conn-data { display: flex; justify-content: space-between; align-items: baseline; .pwr { font-size: 15px; font-weight: bold; color: #333; } .num { font-size: 12px; color: #52c41a; font-weight: bold; } }
		}
	}

	.amenities-box {
		margin-bottom: 25px;
		.a-label { font-size: 13px; font-weight: bold; color: #333; margin-bottom: 12px; display: block; }
		.a-tags { display: flex; flex-wrap: wrap; gap: 8px; }
		.a-tag { display: flex; align-items: center; background: #fff; border: 1px solid #eee; padding: 4px 10px; border-radius: 6px; font-size: 11px; color: #666; text { margin-left: 4px; } }
	}
	
	.action-buttons {
		display: flex; justify-content: space-between;
		.btn-reserve { width: 35%; height: 50px; background: #f0f3ff; border-radius: 25px; color: #0052D9; display: flex; align-items: center; justify-content: center; font-weight: bold; text { margin-left: 6px; } }
		.btn-nav { width: 60%; height: 50px; background: #0052D9; border-radius: 25px; color: #fff; display: flex; align-items: center; justify-content: center; box-shadow: 0 5px 15px rgba(0,82,217,0.3); font-weight: bold; text { margin-left: 8px; font-size: 16px; } }
	}
}
</style>