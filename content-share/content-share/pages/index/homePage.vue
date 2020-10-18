<template>
	<view>
		<view style="position: sticky; top: 0; z-index: 2;">
			<view class="tabBar flex-center">
				<view class="tabBar-child flex-center" :class="{ active: isActive }" @click="changeTab">发现</view>
				<view class="tabBar-child flex-center " :class="{ active: !isActive }" @click="changeTab">使用说明</view>
			</view>
			<view class="lable">{{ notice.content }}</view>
			<view class="search-box"><input type="text" placeholder="请输入关键词" class="input-box" v-model="keywords" @input="getShare()" /></view>
		</view>
		<view class="item-row" v-for="(item, index) in items" :key="index" v-if="isActive">
			<view class="avatar">
				<image :src="item.cover" class="avatar" />
				<view class="avatar-lable" v-if="item.isOriginal == true" style="background-color: #EB544D;color:#F1F1F1;font-size: 14px;">原创</view>
				<view class="avatar-lable" v-else style="background-color: #4CD964;color:#F1F1F1;font-size: 14px;">转载</view>
			</view>
			<view class="content" @click="gotoDetail(item.downloadUrl, item.id)">
				<view class="share-title">{{ item.title.substring(0, 15) }}……</view>
				<view class="content-title">{{ item.summary.substring(0, 15) }}……</view>
			</view>
			<view class="score">
				<view class="content-title">{{ item.price }}积分</view>
				<view class="content-title" v-if="item.downloadUrl == null">兑换</view>
				<view class="content-title" v-else>下载</view>
			</view>
		</view>
		<view class="use-box" v-if="!isActive">
			<view style="font-size: 14px;">资源均为免费，兑换后可查看下载地址;点击我的->兑换，即可查看、下载兑换过的资源。</view>
			<view style=" font-size:15px ;font-weight: 700;">积分获得方式</view>
			<view style="padding-left: 26upx; font-size: 14px;">>每日签到</view>
			<view style="padding-left: 26upx; font-size: 14px;">>投稿</view>
			<view style="padding-left: 26upx; font-size: 14px;">>转发</view>
			<view style="padding-left: 26upx; font-size: 14px;">>提建议、意见(尚未完成)</view>
		</view>
	</view>
</template>

<script>
import { request, get } from '@/utils/request';
import { SHARE_URL, NOTICE_URL, BASE_API_URL } from '@/utils/api';
export default {
	data() {
		return {
			isActive: true,
			items: [],
			notice: {},
			token: null,
			keywords: '',
			pageNo: 1,
			pageSize: 6,
			more: true
		};
	},
	onLoad() {
		uni.showLoading({
			title: '加载中'
		});
		this.getShares(true), this.getNotices();
		setTimeout(() => {
			uni.hideLoading();
		}, 1000);
	},
	onShow() {
		this.getShares(true);
	},
	onReachBottom() {
		//没有更多数据了
		if (!this.more) {
			uni.showToast({
				title: '已加载完毕',
				duration: 1000
			});
			//直接返回，不要执行下面代码了
			return false;
		}
		//正常加载下一页
		this.pageNo = this.pageNo + 1;
		uni.showLoading({
			title: '加载中'
		});
		//不带参数都请求，就是分页加载
		this.getShares();
		setTimeout(() => {
			uni.hideLoading();
		}, 1000);
	},
	//下拉刷新
	onPullDownRefresh() {
		//参数为true，就是重新开始加载
		this.getShares(true);
	},
	methods: {
		changeTab() {
			this.isActive = !this.isActive;
		},
		isHaveToken: function() {
			//如果本地存储没有token对象，取出其token属性的值的类型为undefined，
			console.log('token的类型为》》》》》》》》》》》》》》》》》》》》》' + typeof uni.getStorageSync('token').token);
			console.log('token的值为》》》》》》》》》》》》》》》》》》》》》' + uni.getStorageSync('token').token);
			if (typeof uni.getStorageSync('token').token == 'undefined') {
				this.token = 'no-token';
				console.log('自定义token的值为+++++++++++++++token' + this.token);
			} else {
				this.token = uni.getStorageSync('token').token;
				console.log('自定义token的值为+++++++++++++++token' + this.token);
			}
		},
		getShares: function(init) {
			//入参为true，从第一页重新开始加载数据，场景为：下拉刷新、每次进入
			if (init) {
				this.pageNo = 1;
				this.more = true;
			}
			this.isHaveToken();
			uni.request({
				url: BASE_API_URL + '/shares/query',
				method: 'GET',
				header: {
					'content-type': 'application/json',
					'X-Token': this.token
				},
				data: {
					title: this.keywords,
					pageNo: this.pageNo,
					pageSize: this.pageSize
				},
				success: res => {
					console.log('>>>>>>>>>>>>>>>>' + res.data);
					this.items = res.data.data;
					uni.setStorageSync('items', res.data.data);
					//请求结束延时隐藏加载动画
					setTimeout(() => {
						uni.hideLoading();
					}, 100);
					//处理进入首页加载数据和下拉刷新场景
					if (init) {
						//获得新的数据集合，将数据初始化为本次请求返回结果
						this.items = res.data.data;
						//停止下拉刷新
						uni.stopPullDownRefresh();
					} else {
						//不是下拉刷新，是加载下一页数据，则把新数据追加到后面，不能覆盖原值
						this.items = this.items.concat(res.data);
					}
					//加载到了最后一页
					if (res.data.data.length < this.pageSize && this.pageNo > 0) {
						this.more = false;
					}
				}
			});
		},

		// getShare: function() {
		// 	this.isHaveToken();
		// 	uni.request({
		// 		url: BASE_API_URL + '/shares/query',
		// 		method: 'GET',
		// 		header: {
		// 			'content-type': 'application/json',
		// 			'X-Token': this.token
		// 		},
		// 		data: {
		// 			title: this.keywords
		// 		},
		// 		success: res => {
		// 			console.log('>>>>>>>>>>>>>>>>' + res.data);
		// 			this.items = res.data.data;
		// 			uni.setStorageSync('items', res.data.data);
		// 		}
		// 	});
		// },
		getNotices: function() {
			let self = this;
			uni.request({
				url: BASE_API_URL + '/notices/latest',
				method: 'GET',
				header: {
					'content-type': 'application/json',
					'X-Token': uni.getStorageSync('token').token
				},
				success: res => {
					console.log(res.data);
					self.notice = res.data.data;
					console.log('>>>>>>>>>>>>>>>..' + self.notice);
				}
			});
		},
		gotoDetail: function(downloadUrl, id) {
			console.log('下载地址》》》》》》》》》》》》》》》》' + downloadUrl);
			console.log(id);
			if (downloadUrl == null) {
				uni.navigateTo({
					url: './share-details?id=' + id
				});
			} else {
				uni.navigateTo({
					url: './share-change?id=' + id
				});
			}
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
	position: relative;
	z-index: -1;
}
.avatar-lable {
	position: absolute;
	z-index: 12;
	height: 40upx;
	width: 70upx;
	bottom: 80upx;
	left: -10upx;
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
.use-box {
	display: flex;
	flex-direction: column;
}
.tips-box {
}
</style>
