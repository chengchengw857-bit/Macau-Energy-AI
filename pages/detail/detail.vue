<template>
	<view class="detail-container">
		<u-navbar title="站点详情" :autoBack="true" placeholder></u-navbar>
		
		<!-- 1. 站点顶部图 -->
		<image src="https://cdn.uviewui.com/uview/swiper/swiper2.png" mode="aspectFill" class="top-img"></image>
		
		<!-- 2. 核心信息卡片 -->
		<view class="info-section">
			<view class="header-row">
				<text class="name">乐群楼停车场充电站</text>
				<u-tag text="营业中" type="success" size="mini"></u-tag>
			</view>
			<view class="price-row">
				<text class="label">电费：</text>
				<text class="price">MOP 1.25</text>
				<text class="unit">/度</text>
			</view>
			<view class="parking-info">停车费：前3小时免费，后续 MOP 10/小时</view>
		</view>

		<!-- 3. 终端状态 (模拟真实一体化监控) -->
		<view class="status-section">
			<view class="section-title">终端状态</view>
			<view class="terminal-grid">
				<view class="terminal-item" v-for="i in 4" :key="i">
					<text class="id">#00{{i}}</text>
					<u-icon name="map-fill" :color="i < 3 ? '#52c41a' : '#ff4d4f'" size="30"></u-icon>
					<text class="state">{{i < 3 ? '空闲' : '占用'}}</text>
				</view>
			</view>
		</view>

		<!-- 4. 底部操作栏 -->
		<view class="bottom-bar">
			<u-button type="primary" shape="circle" icon="camera" text="扫码充电" @click="startCharge"></u-button>
		</view>
	</view>
</template>

<script>
export default {
	methods: {
		startCharge() {
			uni.navigateTo({ url: '/pages/charging/charging' });
		}
	}
}
</script>

<style lang="scss">
.top-img { width: 100%; height: 200px; }
.info-section { padding: 20px; background: #fff; border-bottom: 10px solid #f5f5f5;
	.name { font-size: 20px; font-weight: bold; }
	.price-row { margin-top: 10px; .price { font-size: 24px; color: #ff9900; font-weight: bold; } .unit { font-size: 14px; color: #999; } }
	.parking-info { font-size: 13px; color: #666; margin-top: 10px; }
}
.status-section { padding: 20px; .section-title { font-weight: bold; margin-bottom: 15px; } }
.terminal-grid { display: flex; justify-content: space-around;
	.terminal-item { text-align: center; display: flex; flex-direction: column; background: #fff; padding: 10px; border-radius: 8px; width: 20%; .id { font-size: 12px; margin-bottom: 5px; } .state { font-size: 12px; margin-top: 5px; } }
}
.bottom-bar { position: fixed; bottom: 0; left: 0; right: 0; padding: 20px; background: #fff; box-shadow: 0 -2px 10px rgba(0,0,0,0.05); }
</style>