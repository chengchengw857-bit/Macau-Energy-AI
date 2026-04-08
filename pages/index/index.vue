<template>
	<view class="container">
		<!-- 1. 沉浸式顶部 -->
		<view class="header-box">
			<view class="tech-bg"><view class="grid-overlay"></view></view>
			<view class="header-content">
				<view class="status-info">
					<view class="left-status">
						<u-icon name="map-fill" color="rgba(255,255,255,0.7)" size="14"></u-icon>
						<text class="status-text">澳门 · 晴 22℃</text>
					</view>
					<view class="lang-picker" @click="showLangSheet = true">
						<text class="lang-txt">{{ langText }}</text>
						<u-icon name="arrow-down-fill" color="rgba(255,255,255,0.5)" size="10"></u-icon>
					</view>
					<view class="right-status">
						<text class="status-text">已连接 V2G</text>
						<view class="online-dot"></view>
					</view>
				</view>

				<view class="user-row">
					<view class="user-main">
						<text class="main-t">{{ i18n[langKey].welcome }}</text>
						<view class="car-status-tag">
							<text>Tesla Model 3</text>
							<view class="v-line"></view>
							<text>续航 82km</text>
						</view>
					</view>
					<view class="scan-box" @click="handleScan">
						<u-icon name="scan" color="#fff" size="28"></u-icon>
						<text class="scan-lab">扫码加电</text>
					</view>
				</view>
				
				<view class="ai-glass-card" @click="menuJump(0)">
					<view class="ai-header">
						<view class="ai-title"><view class="pulse-dot"></view><text>AI 智慧补能建议</text></view>
						<text class="ai-time">刚刚更新</text>
					</view>
					<view class="ai-body">
						<view class="ai-info">
							<text class="ai-main-text">{{ i18n[langKey].ai_adv }}</text>
							<text class="ai-sub-text">预计排队 15 分钟 · 预计节省费用 MOP 15.2</text>
						</view>
						<view class="ai-go-btn"><text>前往</text><u-icon name="arrow-rightward" color="#000" size="14"></u-icon></view>
					</view>
				</view>
			</view>
		</view>

		<!-- 2. 核心功能矩阵 -->
		<view class="card-wrap toolbox-card">
			<u-grid :border="false" col="4">
				<u-grid-item v-for="(item, index) in toolbox" :key="index" customStyle="padding: 12px 0" @click="menuJump(index)">
					<view class="icon-circle" :style="{backgroundColor: item.bg}">
						<u-icon :name="item.icon" :size="24" :color="item.color"></u-icon>
					</view>
					<text class="grid-text">{{item.title}}</text>
				</u-grid-item>
			</u-grid>
		</view>

		<!-- 3. 王牌特色服务 (已修改文字并修复图标) -->
		<view class="featured-section">
			<view class="section-title">王牌特色服务</view>
			<view class="feature-flex">
				<!-- 修改1：换电“外卖小哥” -> 换电“外卖服务” -->
				<view class="feature-item delivery" @click="handleFeatured('takeaway')">
					<view class="f-info">
						<text class="f-t">换电“外卖服务”</text>
						<text class="f-s">送电上门 · 解决1公里</text>
					</view>
					<u-icon name="car-fill" color="rgba(255,255,255,0.4)" size="36"></u-icon>
				</view>
				<!-- 修改2：flash-line 换成内置图标 integral-fill -->
				<view class="feature-item rescue" @click="handleFeatured('rescue')">
					<view class="f-info">
						<text class="f-t">应急“救援补给”</text>
						<text class="f-s">极速响应 · 电车续命</text>
					</view>
					<u-icon name="integral-fill" color="rgba(255,255,255,0.4)" size="38"></u-icon>
				</view>
			</view>
		</view>

		<!-- 4. 能量资产 -->
		<view class="card-wrap energy-card">
			<view class="card-title">能量资产状态</view>
			<view class="energy-content">
				<view class="data-item"><text class="num">156.4</text><text class="unit">度</text><text class="label">累计已用</text></view>
				<view class="line"></view>
				<view class="data-item"><text class="num primary">3,200</text><text class="unit">分</text><text class="label">绿能积分</text></view>
				<view class="action-btn"><u-button type="primary" size="small" shape="circle" text="积分兑换"></u-button></view>
			</view>
		</view>

		<u-action-sheet :actions="langList" :show="showLangSheet" @close="showLangSheet = false" @select="onLangSelect" cancelText="取消"></u-action-sheet>
	</view>
</template>

<script>
export default {
	data() {
		return {
			showLangSheet: false, langText: '繁體', langKey: 'zh_tw',
			langList: [{ name: '简体中文', value: 'zh_cn' }, { name: '繁體中文', value: 'zh_tw' }, { name: 'English', value: 'en' }],
			i18n: {
				zh_cn: { welcome: '早安，亲爱的车主', ai_adv: '最近的“乐群楼换电站”空闲5座' },
				zh_tw: { welcome: '早安，亲爱的車主', ai_adv: '最近的“樂群樓換電站”空閒5座' },
				en: { welcome: 'Morning, Wang', ai_adv: 'Nearest "Seac Pai Van" has 5 slots' }
			},
			toolbox: [
				{ title: 'AI智能地图', icon: 'map', color: '#0052D9', bg: '#eef3ff' },
				{ title: 'AI一站式预约', icon: 'calendar', color: '#ff9900', bg: '#fff5e6' },
				{ title: 'AI智慧路径', icon: 'list-dot', color: '#00b373', bg: '#e6fff1' },
				{ title: 'AR导航', icon: 'camera-fill', color: '#722ed1', bg: '#f9f0ff' },
				{ title: '应急补给', icon: 'car-fill', color: '#ff4d4f', bg: '#fff1f0' },
				{ title: '互动社区', icon: 'chat-fill', color: '#5ac725', bg: '#dbfbdb' },
				{ title: '积分任务', icon: 'gift-fill', color: '#13c2c2', bg: '#e6fffb' },
				{ title: '资助计划', icon: 'heart-fill', color: '#eb2f96', bg: '#fff0f6' }
			]
		}
	},
	methods: {
		handleScan() { uni.scanCode({ success: () => uni.navigateTo({ url: '/pages/charging/charging' }) }); },
		onLangSelect(e) { this.langKey = e.value; this.langText = e.name.substring(0, 2); },
		menuJump(idx) {
			const routes = { 0: '/pages/map/map', 1: '/pages/reserve/reserve', 2: '/pages/route/route', 3: '/pages/ar/ar', 4: '/pages/rescue/rescue', 5: '/pages/news/news' };
			const url = routes[idx];
			if (url) { (idx === 0 || idx === 5) ? uni.switchTab({ url }) : uni.navigateTo({ url }); }
		},
		handleFeatured(type) {
			const url = type === 'takeaway' ? '/pages/takeaway/takeaway' : '/pages/rescue/rescue';
			uni.navigateTo({ url });
		}
	}
}
</script>

<style lang="scss" scoped>
.container { background-color: #f7f8fa; min-height: 100vh; padding-bottom: 40px; }
.header-box { background: #000; height: 340px; position: relative; overflow: hidden;
	.tech-bg { position: absolute; top: 0; left: 0; right: 0; bottom: 0;
		.grid-overlay { position: absolute; top: 0; left: 0; right: 0; bottom: 0; background-image: linear-gradient(rgba(255,255,255,0.05) 1px, transparent 1px), linear-gradient(90deg, rgba(255,255,255,0.05) 1px, transparent 1px); background-size: 20px 20px; }
	}
	.header-content { padding: 50px 20px 0; position: relative; z-index: 10; }
}
.status-info { display: flex; justify-content: space-between; align-items: center; margin-bottom: 25px; .status-text { color: rgba(255,255,255,0.6); font-size: 11px; } .online-dot { width: 6px; height: 6px; background: #52c41a; border-radius: 50%; box-shadow: 0 0 8px #52c41a; } }
.lang-picker { display: flex; align-items: center; background: rgba(255, 255, 255, 0.15); border: 1px solid rgba(255, 255, 255, 0.1); padding: 2px 10px; border-radius: 20px; .lang-txt { color: #fff; font-size: 11px; margin-right: 4px; } }
.user-row { display: flex; justify-content: space-between; .main-t { font-size: 22px; font-weight: bold; color: #fff; } .car-status-tag { margin-top: 8px; background: rgba(255,255,255,0.1); padding: 4px 10px; border-radius: 4px; display: flex; color: rgba(255,255,255,0.8); font-size: 11px; .v-line { width: 1px; height: 10px; background: rgba(255,255,255,0.2); margin: 0 10px; } } .scan-box { text-align: center; color: #fff; .scan-lab { font-size: 10px; display: block; margin-top: 5px; } } }
.ai-glass-card { margin-top: 30px; background: rgba(255,255,255,0.08); border: 1px solid rgba(255,255,255,0.15); backdrop-filter: blur(15px); border-radius: 18px; padding: 18px; 
	.ai-header { display: flex; justify-content: space-between; .ai-title { display: flex; align-items: center; color: #fff; font-size: 13px; font-weight: bold; .pulse-dot { width: 8px; height: 8px; background: #2979ff; border-radius: 50%; margin-right: 8px; animation: pulse 2s infinite; } } .ai-time { color: rgba(255,255,255,0.4); font-size: 10px; } } 
	.ai-body { display: flex; justify-content: space-between; align-items: center; margin-top: 15px; .ai-main-text { color: #fff; font-size: 14px; font-weight: bold; } .ai-sub-text { color: rgba(255,255,255,0.5); font-size: 11px; margin-top: 5px; display: block; } .ai-go-btn { background: #fff; padding: 6px 15px; border-radius: 20px; font-size: 12px; font-weight: bold; } }
}
@keyframes pulse { 0% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(41, 121, 255, 0.7); } 70% { transform: scale(1); box-shadow: 0 0 0 10px rgba(41, 121, 255, 0); } 100% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(41, 121, 255, 0); } }
.toolbox-card { margin: -30px 15px 15px; background: #fff; border-radius: 16px; padding: 15px; position: relative; z-index: 20; box-shadow: 0 8px 20px rgba(0,0,0,0.05); }
.icon-circle { width: 42px; height: 42px; border-radius: 50%; display: flex; align-items: center; justify-content: center; margin-bottom: 8px; }
.grid-text { font-size: 11px; color: #666; }
.featured-section { padding: 10px 15px; .section-title { font-size: 16px; font-weight: bold; margin-bottom: 12px; } 
	.feature-flex { display: flex; justify-content: space-between; 
		.feature-item { width: 48.5%; height: 95px; border-radius: 16px; padding: 15px; position: relative; overflow: hidden; display: flex; flex-direction: column; justify-content: space-between; 
			.f-info { z-index: 2; .f-t { color: #fff; font-size: 14px; font-weight: bold; display: block; } .f-s { color: rgba(255,255,255,0.6); font-size: 10px; margin-top: 4px; display: block; } } 
			::v-deep .u-icon { position: absolute !important; right: -5px !important; bottom: -5px !important; z-index: 1 !important; }
			&.delivery { background: linear-gradient(135deg, #2c3e50, #000); }
			&.rescue { background: linear-gradient(135deg, #ff4e50, #f9d423); }
		}
	}
}
.card-wrap { margin: 15px; background: #fff; border-radius: 16px; padding: 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.03); 
	.card-title { font-size: 15px; font-weight: bold; margin-bottom: 15px; } 
	.energy-content { display: flex; align-items: center; justify-content: space-between; 
		.data-item { flex: 1; .num { font-size: 20px; font-weight: bold; &.primary { color: #0052D9; } } .unit { font-size: 11px; color: #999; margin-left: 4px; } .label { font-size: 11px; color: #999; margin-top: 4px; display: block; } } 
		.line { width: 1px; height: 30px; background: #eee; margin: 0 15px; }
	}
}
</style>