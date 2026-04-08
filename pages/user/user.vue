<template>
	<view class="user-page">
		<!-- 1. 顶部用户身份区：增加 VIP 徽章 -->
		<view class="user-section">
			<view class="avatar-wrap">
				<u-avatar src="https://cdn.uviewui.com/uview/album/1.jpg" size="65"></u-avatar>
				<view class="vip-badge">
					<u-icon name="v-fill" color="#fff" size="12"></u-icon>
				</view>
			</view>
			<view class="user-info">
				<view class="name-row">
					<text class="nickname">亲爱的车主</text>
					<u-tag text="铂金会员" size="mini" type="warning" shape="circle" customStyle="margin-left:8px; background: linear-gradient(90deg, #fadb14, #ff9900); border:none; color:#fff"></u-tag>
				</view>
				<text class="car-id">Tesla Model 3 · MP-88-88</text>
			</view>
			<view class="set-icon">
				<u-icon name="setting" size="24" color="#333"></u-icon>
			</view>
		</view>

		<!-- 2. VIP 会员黑金卡片 (新增：大厂高级感核心) -->
		<view class="vip-card">
			<view class="card-left">
				<view class="v-title">Macau Energy <text class="v- VIP">VIP</text></view>
				<view class="v-desc">享全澳换电服务费 8 折优惠</view>
			</view>
			<view class="v-btn">立即续费</view>
			<!-- 装饰背景 -->
			<view class="v-circle"></view>
		</view>

		<!-- 3. 资产与绿能数据看板 (新增：体现社会价值) -->
		<view class="stats-card">
			<view class="stats-grid">
				<view class="stats-item">
					<text class="num">1544.50</text>
					<text class="lab">余额(MOP)</text>
				</view>
				<view class="divider"></view>
				<view class="stats-item">
					<text class="num highlight">3,200</text>
					<text class="lab">绿能积分</text>
				</view>
				<view class="divider"></view>
				<view class="stats-item">
					<text class="num green">842</text>
					<text class="lab">减碳排放(kg)</text>
				</view>
			</view>
		</view>

		<!-- 4. 车辆状态卡片 (新增：实时联动感) -->
		<view class="car-status-card">
			<view class="car-header">
				<text>车辆状态</text>
				<u-tag text="已连接" size="mini" type="success" plain></u-tag>
			</view>
			<view class="car-body">
				<view class="b-item">
					<u-icon name="battery-full" color="#52c41a" size="24"></u-icon>
					<view class="b-info">
						<text class="b-v">82%</text>
						<text class="b-l">剩余电量</text>
					</view>
				</view>
				<view class="b-item">
					<u-icon name="map" color="#2979ff" size="24"></u-icon>
					<view class="b-info">
						<text class="b-v">356km</text>
						<text class="b-l">可续航里程</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 5. 业务列表：分组展示 -->
		<view class="menu-group">
			<u-cell-group :border="false">
				<u-cell icon="order" title="充电/换电记录" isLink></u-cell>
				<u-cell icon="coupon" title="我的优惠券" value="2张" isLink></u-cell>
				<u-cell icon="map" title="常用地址管理" isLink></u-cell>
			</u-cell-group>
		</view>

		<view class="menu-group">
			<u-cell-group :border="false">
				<u-cell icon="kefu-erfon" title="在线智能客服" isLink></u-cell>
				<u-cell icon="question-circle" title="帮助与反馈" isLink></u-cell>
				<u-cell icon="share-fill" title="邀请好友领积分" isLink></u-cell>
			</u-cell-group>
		</view>
		
		<!-- 6. 车主互动社区 -->
		<view class="community-section">
			<view class="section-header">
				<text class="section-title">车主社区</text>
				<text class="section-more" @click="viewMoreCommunity">查看更多</text>
			</view>
			
			<!-- 充电站点评与分享 -->
			<view class="review-card">
				<view class="review-header">
					<u-avatar src="https://cdn.uviewui.com/uview/album/3.jpg" size="40"></u-avatar>
					<view class="review-info">
						<text class="review-name">澳门车主</text>
						<text class="review-time">2小时前</text>
					</view>
					<u-tag text="已认证" size="mini" type="success" plain></u-tag>
				</view>
				<text class="review-content">乐群楼换电站服务很棒，换电速度快，环境整洁，推荐给大家！ #换电体验 #澳门绿色出行</text>
				<view class="review-image">
					<image src="https://images.unsplash.com/photo-1593941707882-a5bba14938c7?w=200&q=80" mode="aspectFill"></image>
				</view>
				<view class="review-footer">
					<view class="review-action">
						<u-icon name="heart" size="16" color="#999"></u-icon>
						<text>128</text>
					</view>
					<view class="review-action">
						<u-icon name="chat" size="16" color="#999"></u-icon>
						<text>42</text>
					</view>
					<view class="review-action">
						<u-icon name="share" size="16" color="#999"></u-icon>
						<text>分享</text>
					</view>
				</view>
			</view>
			
			<!-- 资讯与官方发布 -->
			<view class="news-card">
				<view class="news-header">
					<text class="news-title">最新资讯</text>
				</view>
				<view class="news-item" v-for="(item, index) in newsList" :key="index">
					<text class="news-content">{{item.title}}</text>
					<text class="news-time">{{item.time}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			newsList: [
				{ title: '澳门电力推出新的峰谷电价政策，夜间充电更优惠', time: '2026-03-08' },
				{ title: '路环新换电站即将投入使用，预计4月正式运营', time: '2026-03-07' },
				{ title: '特斯拉Model Y在澳门销量突破1000台，绿色出行成趋势', time: '2026-03-06' }
			]
		}
	},
	methods: {
		viewMoreCommunity() {
			// 导航到社区详情页
			uni.showToast({
				title: '查看更多社区内容',
				icon: 'none',
				duration: 1000
			});
		}
	}
}
</script>

<style lang="scss" scoped>
.user-page { background: #f7f8fa; min-height: 100vh; padding-bottom: 30px; }

/* 用户身份区 */
.user-section {
	padding: 60px 20px 20px; display: flex; align-items: center; background: #fff;
	.avatar-wrap {
		position: relative;
		.vip-badge {
			position: absolute; bottom: 0; right: 0; background: #ff9900; border-radius: 50%;
			width: 18px; height: 18px; display: flex; align-items: center; justify-content: center;
			border: 2px solid #fff;
		}
	}
	.user-info {
		flex: 1; margin-left: 15px;
		.nickname { font-size: 20px; font-weight: bold; color: #333; }
		.car-id { font-size: 12px; color: #999; margin-top: 4px; display: block; }
	}
}

/* VIP黑金卡片 */
.vip-card {
	margin: 15px; background: linear-gradient(135deg, #333, #000); border-radius: 16px;
	padding: 20px; position: relative; overflow: hidden; display: flex; justify-content: space-between; align-items: center;
	.card-left {
		z-index: 2;
		.v-title { color: #fadb14; font-size: 18px; font-weight: bold; .v-plus { font-style: italic; margin-left: 5px; } }
		.v-desc { color: rgba(250, 219, 20, 0.7); font-size: 11px; margin-top: 5px; }
	}
	.v-btn {
		z-index: 2; background: #fadb14; color: #000; padding: 6px 15px; border-radius: 20px; font-size: 12px; font-weight: bold;
	}
	.v-circle {
		position: absolute; right: -20px; top: -20px; width: 100px; height: 100px;
		background: rgba(250, 219, 20, 0.1); border-radius: 50%; z-index: 1;
	}
}

/* 统计看板 */
.stats-card {
	margin: 0 15px 15px; background: #fff; border-radius: 16px; padding: 20px;
	.stats-grid {
		display: flex; justify-content: space-around; align-items: center;
		.stats-item {
			text-align: center;
			.num { font-size: 20px; font-weight: bold; display: block; &.highlight { color: #ff9900; } &.green { color: #52c41a; } }
			.lab { font-size: 11px; color: #999; margin-top: 4px; }
		}
		.divider { width: 1px; height: 20px; background: #eee; }
	}
}

/* 车辆状态卡片 */
.car-status-card {
	margin: 0 15px 15px; background: #fff; border-radius: 16px; padding: 15px;
	.car-header { display: flex; justify-content: space-between; font-size: 14px; font-weight: bold; margin-bottom: 15px; }
	.car-body {
		display: flex; justify-content: space-around;
		.b-item {
			display: flex; align-items: center;
			.b-info { margin-left: 10px; .b-v { font-size: 16px; font-weight: bold; display: block; } .b-l { font-size: 10px; color: #999; } }
		}
	}
}

/* 菜单分组 */
.menu-group { margin: 15px; background: #fff; border-radius: 16px; overflow: hidden; box-shadow: 0 4px 12px rgba(0,0,0,0.02); }

/* 车主互动社区 */
.community-section {
	margin: 15px;
	
	.section-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 15px;
		
		.section-title {
			font-size: 16px;
			font-weight: bold;
			color: #333;
		}
		
		.section-more {
			font-size: 12px;
			color: #2979ff;
			cursor: pointer;
		}
	}
	
	.review-card {
		background: #fff;
		border-radius: 16px;
		padding: 15px;
		margin-bottom: 15px;
		box-shadow: 0 4px 12px rgba(0,0,0,0.02);
		
		.review-header {
			display: flex;
			align-items: center;
			margin-bottom: 12px;
			
			.review-info {
				flex: 1;
				margin-left: 10px;
				
				.review-name {
					font-size: 14px;
					font-weight: bold;
					color: #333;
				}
				
				.review-time {
					font-size: 11px;
					color: #999;
					margin-top: 2px;
					display: block;
				}
			}
		}
		
		.review-content {
			font-size: 13px;
			color: #666;
			line-height: 1.5;
			margin-bottom: 12px;
		}
		
		.review-image {
			margin-bottom: 12px;
			
			image {
				width: 100%;
				height: 120px;
				border-radius: 8px;
			}
		}
		
		.review-footer {
			display: flex;
			align-items: center;
			
			.review-action {
				display: flex;
				align-items: center;
				margin-right: 20px;
				
				text {
					font-size: 12px;
					color: #999;
					margin-left: 4px;
				}
			}
		}
	}
	
	.news-card {
		background: #fff;
		border-radius: 16px;
		padding: 15px;
		box-shadow: 0 4px 12px rgba(0,0,0,0.02);
		
		.news-header {
			margin-bottom: 12px;
			
			.news-title {
				font-size: 14px;
				font-weight: bold;
				color: #333;
			}
		}
		
		.news-item {
			padding: 10px 0;
			border-bottom: 1px solid #f0f0f0;
			
			&:last-child {
				border-bottom: none;
			}
			
			.news-content {
				font-size: 13px;
				color: #666;
				display: block;
				margin-bottom: 4px;
			}
			
			.news-time {
				font-size: 11px;
				color: #999;
			}
		}
	}
}
</style>