<template>
	<view class="discover-wrap">
		<!-- 1. 沉浸式顶部：高端科技看板 -->
		<view class="dashboard-header">
			<view class="bg-pattern">
				<view class="flare"></view>
			</view>
			<view class="header-content">
				<view class="brand-line">
					<view class="logo-circle">
						<u-icon name="map-fill" color="#fff" size="14"></u-icon>
					</view>
					<text class="brand-text">MACAU GREEN ENERGY NETWORK</text>
				</view>
				
				<view class="main-stats">
					<text class="label">“澳”游电能实时累计加电 (次)</text>
					<view class="counter-row">
						<u-count-to :startVal="99120000" :endVal="99129457" :duration="2000" color="#fff" fontSize="42" bold></u-count-to>
					</view>
				</view>

				<!-- 资产卡片：极致毛玻璃悬浮感 -->
				<view class="asset-glass">
					<view class="asset-item">
						<text class="v">12 <text class="u">座</text></text>
						<text class="l">智慧换电站</text>
					</view>
					<view class="sep"></view>
					<view class="asset-item">
						<text class="v">27,957 <text class="u">根</text></text>
						<text class="l">联网充电桩</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 2. 精选活动轮播：解决图片不显示问题，采用高清稳定源 -->
		<view class="swiper-section">
			<u-swiper
				:list="swiperList"
				keyName="image"
				showTitle
				circular
				radius="16"
				height="180"
				indicator
				indicatorMode="line"
			></u-swiper>
		</view>

		<!-- 3. 减碳贡献报告 -->
		<view class="card-section">
			<view class="section-header">
				<text class="h-title">绿能减碳报告</text>
				<u-icon name="arrow-right" size="14" color="#999"></u-icon>
			</view>
			<view class="carbon-card">
				<view class="card-left">
					<image src="https://images.unsplash.com/photo-1558449028-b53a39d100fc?w=200&q=80" mode="aspectFill" class="carbon-icon"></image>
					<view>
						<text class="c-num">8,412.5</text>
						<text class="c-unit">累计减碳 (吨)</text>
					</view>
				</view>
				<view class="card-right">
					<text class="p-text">全澳绿能覆盖率 65%</text>
					<u-line-progress :percentage="65" activeColor="#2979ff" height="8" :showText="false"></u-line-progress>
				</view>
			</view>
		</view>

		<!-- 4. 绿能知识百科：大图瀑布流 -->
		<view class="card-section">
			<view class="section-header">
				<text class="h-title">绿能百科</text>
			</view>
			<view class="article-flex">
				<view class="article-card" v-for="(item, index) in articles" :key="index">
					<image :src="item.image" mode="aspectFill" class="art-img"></image>
					<view class="art-tag">{{item.tag}}</view>
					<view class="art-content">
						<text class="art-t">{{item.title}}</text>
						<text class="art-d">{{item.desc}}</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 5. 社区动态：采用 Ins 风格大图布局 -->
		<view class="card-section">
			<view class="section-header">
				<text class="h-title">车主动态</text>
				<text class="h-sub">查看全部</text>
			</view>
			<view class="post-card">
				<view class="p-header">
					<u-avatar src="https://images.unsplash.com/photo-1535713875002-d1d0cf377fde?w=100&q=80" size="30"></u-avatar>
					<view class="p-u-info">
						<text class="p-name">Macau_Driver_诚成</text>
						<text class="p-time">2小时前 · 乐群楼换电站</text>
					</view>
					<u-tag text="已换电" size="mini" type="success" plain></u-tag>
				</view>
				<text class="p-content">澳门的换电速度真的快！3分钟满电出发，完全没有续航焦虑。 #澳门绿色出行 #蔚来能源</text>
				<image src="https://images.unsplash.com/photo-1593941707882-a5bba14938c7?w=800&q=80" mode="aspectFill" class="p-main-img"></image>
				<view class="p-footer">
					<view class="p-action"><u-icon name="heart" size="20"></u-icon><text>128</text></view>
					<view class="p-action"><u-icon name="chat" size="20"></u-icon><text>42</text></view>
				</view>
			</view>
		</view>

		<view class="safe-area-inset-bottom"></view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			swiperList: [
				{ 
					image: 'https://images.unsplash.com/photo-1506744038136-46273834b3fb?w=800&q=80', 
					title: '澳门智慧能源周：开启低碳新出行' 
				},
				{ 
					image: 'https://images.unsplash.com/photo-1497436072909-60f360e1d4b1?w=800&q=80', 
					title: 'V2G反向供电：您的爱车能赚钱' 
				},
				{ 
					image: 'https://images.unsplash.com/photo-1518770660439-4636190af475?w=800&q=80', 
					title: '新站点：路环乐群楼换电站现已开业' 
				}
			],
			articles: [
				{ 
					title: '电池租用服务解析', 
					desc: '深度解析 BaaS 服务逻辑...', 
					tag: '官方', 
					image: 'https://images.unsplash.com/photo-1594535182308-8ffefbb661e1?w=400&q=80' 
				},
				{ 
					title: '冬季续航指南', 
					desc: '澳门车主必看...', 
					tag: '技术', 
					image: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=400&q=80' 
				}
			]
		}
	}
}
</script>

<style lang="scss" scoped>
.discover-wrap { background: #f4f7fa; min-height: 100vh; padding-bottom: 40px; }

/* 1. 科技看板美化 */
.dashboard-header {
	background: linear-gradient(180deg, #000 0%, #0a1128 100%); 
	height: 340px; position: relative; overflow: hidden;
	.bg-pattern {
		position: absolute; width: 100%; height: 100%;
		background-image: radial-gradient(circle at 80% 20%, rgba(41, 121, 255, 0.2) 0%, transparent 60%);
		.flare {
			position: absolute; top: 10%; right: 10%; width: 180px; height: 180px;
			background: radial-gradient(circle, rgba(41, 121, 255, 0.12), transparent 70%);
			animation: breathe 4s infinite alternate;
		}
	}
	.header-content { padding: 60px 25px 0; position: relative; z-index: 10; }
	.brand-line {
		display: flex; align-items: center; margin-bottom: 25px;
		.logo-circle { width: 26px; height: 26px; background: #2979ff; border-radius: 50%; display: flex; align-items: center; justify-content: center; box-shadow: 0 0 15px rgba(41, 121, 255, 0.5); }
		.brand-text { color: #fff; font-size: 10px; font-weight: bold; letter-spacing: 2px; margin-left: 10px; opacity: 0.8; }
	}
	.main-stats {
		.label { color: rgba(255,255,255,0.4); font-size: 13px; display: block; }
		.counter-row { margin-top: 10px; }
	}
}

@keyframes breathe { from { opacity: 0.3; transform: scale(1); } to { opacity: 0.6; transform: scale(1.3); } }

/* 资产毛玻璃卡片 */
.asset-glass {
	margin-top: 40px; background: rgba(255,255,255,0.06); backdrop-filter: blur(25px);
	border: 1px solid rgba(255,255,255,0.1); border-radius: 20px; padding: 22px;
	display: flex; align-items: center;
	.asset-item {
		flex: 1; text-align: center;
		.v { color: #fff; font-size: 22px; font-weight: bold; display: block; .u { font-size: 11px; margin-left: 4px; opacity: 0.5; font-weight: normal; } }
		.l { color: rgba(255,255,255,0.4); font-size: 11px; margin-top: 6px; }
	}
	.sep { width: 1px; height: 35px; background: rgba(255,255,255,0.1); }
}

/* 轮播图 */
.swiper-section { margin: -30px 15px 20px; position: relative; z-index: 20; box-shadow: 0 12px 30px rgba(0,0,0,0.15); }

/* 通用标题 */
.card-section { margin: 25px 15px; }
.section-header {
	display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px;
	.h-title { font-size: 18px; font-weight: bold; color: #1a1a1a; }
	.h-sub { font-size: 12px; color: #2979ff; }
}

/* 减碳报告卡片 */
.carbon-card {
	background: #fff; border-radius: 20px; padding: 20px; display: flex; align-items: center; justify-content: space-between;
	box-shadow: 0 4px 15px rgba(0,0,0,0.02);
	.card-left {
		display: flex; align-items: center;
		.carbon-icon { width: 44px; height: 44px; border-radius: 12px; margin-right: 15px; }
		.c-num { font-size: 24px; font-weight: 800; color: #1a1a1a; display: block; }
		.c-unit { font-size: 11px; color: #999; }
	}
	.card-right { width: 140px; .p-text { font-size: 11px; color: #666; margin-bottom: 8px; display: block; text-align: right; } }
}

/* 知识百科布局 */
.article-flex {
	display: flex; justify-content: space-between;
	.article-card {
		width: 48.5%; background: #fff; border-radius: 16px; overflow: hidden; position: relative;
		box-shadow: 0 4px 12px rgba(0,0,0,0.03);
		.art-img { width: 100%; height: 120px; }
		.art-tag { position: absolute; top: 10px; right: 10px; background: rgba(0,0,0,0.5); color: #fff; font-size: 10px; padding: 2px 8px; border-radius: 4px; backdrop-filter: blur(4px); }
		.art-content { padding: 12px; .art-t { font-size: 14px; font-weight: bold; color: #333; display: block; } .art-d { font-size: 11px; color: #999; margin-top: 4px; display: block; } }
	}
}

/* 社区卡片美化 */
.post-card {
	background: #fff; border-radius: 20px; padding: 20px; box-shadow: 0 4px 20px rgba(0,0,0,0.03);
	.p-header {
		display: flex; align-items: center; margin-bottom: 15px;
		.p-u-info { flex: 1; margin-left: 12px; .p-name { font-size: 14px; font-weight: bold; color: #333; display: block; } .p-time { font-size: 11px; color: #999; } }
	}
	.p-content { font-size: 14px; color: #444; line-height: 1.6; margin-bottom: 15px; display: block; }
	.p-main-img { width: 100%; height: 200px; border-radius: 12px; margin-bottom: 15px; }
	.p-footer {
		display: flex; border-top: 1px solid #f5f5f5; padding-top: 15px;
		.p-action { display: flex; align-items: center; margin-right: 30px; color: #666; text { font-size: 13px; margin-left: 6px; } }
	}
}
</style>