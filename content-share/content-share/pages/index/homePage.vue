<template>
	<view>
		<view style="position: sticky; top: 0; z-index: 2;">
			<view class="tabBar flex-center">
				<view class="tabBar-child flex-center" :class="{ active: isActive }" @click="changeTab">发现</view>
				<view class="tabBar-child flex-center " :class="{ active: !isActive }" @click="changeTab">使用说明</view>
			</view>
			<view class="lable">{{ notice.content }}</view>
			<view class="search-box"><input type="text" placeholder="请输入关键词" class="input-box" /></view>
		</view>
		<view class="item-row" v-for="(item, index) in items" :key="index">
			<view class="avatar"><image :src="item.cover" class="avatar" /></view>
			<view class="content">
				<view class="share-title">{{ item.title }}</view>
				<view class="content-title">{{ item.summary.substring(0, 25) }}……</view>
			</view>
			<view class="score">
				<view class="content-title">{{ item.price }}积分</view>
				<view class="content-title">兑换</view>
			</view>
		</view>
	</view>
</template>

<script>
import { request, get } from '@/utils/request';
import { SHARE_URL, NOTICE_URL } from '@/utils/api';
export default {
	data() {
		return {
			isActive: true,
			items: [],
			notice: {	
			}
		};
	},
	onLoad() {
		this.getShares(), this.getNotice();
	},
	methods: {
		changeTab() {
			this.isActive = !this.isActive;
		},
		async getShares() {
			//shares列表
			console.log('数组长度为' + this.items.length);
			let res = await get(SHARE_URL);
			this.items = res.data;
			console.log(this.items);
			console.log('数组长度为' + this.items.length);
		},
		async getNotice() {
			//公告
			let notice = await get(NOTICE_URL);
			console.log(notice.data);
			this.notice = notice.data;
		},
		getShare: function() {
			console.log("调用列表数据")
			uni.request({
				url: 'http://120.26.184.121:9002/shares/query',
				method: 'GET',
				header: {
					'content-type': 'application/json'
				},
				success: res => {
					console.log("进入回调函数")
					this.items = res.data.data;
				}
			});
		},
		getNotices: function() {
			let self = this;
			uni.request({
				url: 'http://120.26.184.121:9002/notices/latest',
				method: 'GET',
				header: {
					'content-type': 'application/json'
				},
				success: res => {
					self.notice = res.data;
					console.log(">>>>>>>>>>>>>>>.."+self.notice)
				}
			});   
		}
	}
};
</script>

<style scoped>
.tabBar {
	background-color: #ffffff;
	height: 80upx;
}
.tabBar-child {
	height: 100%;
	width: 50%;
	font-size: 16px;
	color: #8c8c8c;
}
.flex-center {
	display: flex;
	justify-content: center;
	align-items: center;
}
.flex-around {
	display: flex;
	justify-content: space-around;
	align-items: center;
}
.active {
	color: black;
	font-weight: 400;
	border-bottom: #fe4543 solid 2px;
}
.lable {
	display: flex;
	align-items: center;
	height: 60upx;
	background-color: #fefce5;
	font-size: 12px;
	font-weight: 400;
	color: #e4883b;
}
.search-box {
	height: 45upx;
	background-color: #f2f2f2;
	padding: 15upx;
}
.input-box {
	height: 25upx;
	padding: 4upx;
	background-color: #ffffff;
	font-size: 12px;
	color: #727272;
	width: 95%;
}
.item-row {
	height: 160upx;
	padding: 20upx;
	display: flex;
	justify-content: space-around;
	border-bottom: 1px solid #dfdfdf;
}
.avatar {
	width: 120upx;
	height: 120upx;
	border-radius: 50%;
}
.content {
	width: 60%;
	display: flex;
	flex-direction: column;
}
.score {
	width: 15%;
	display: flex;
	flex-direction: column;
}
.share-title {
	font-weight: 600;
	font-size: 14px;
}
.content-title {
	font-size: 13px;
}
</style>
