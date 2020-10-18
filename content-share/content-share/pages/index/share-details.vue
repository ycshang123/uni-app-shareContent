<template>
	<view>
		<view class="title-box">{{ share.title }}</view>
		<view class="detail-box">
			<view>作者:{{ share.author }}</view>
			<view>发布人:{{share.author}}</view>
			<view>下载数:{{ share.buyCount }}</view>
		</view>
		<view class="content-box">{{ share.summary }}</view>
		<view class="bottom-box">
			<view class="bottom-inner-box">
				<view style="position: absolute;padding-right: 200upx;bottom: 20upx;">积分:{{ share.price }}</view>
				<button class="red-button" @click="exchange()">兑换</button>
			</view>
		</view>
	</view>
</template>

<script>
import { request } from '@/utils/request';
import { BASE_API_URL } from '@/utils/api';
export default {
	data() {
		return {
			share: null,
		};
	},
	onLoad(option) {
		let id = option.id;
		this.getShare(id);
	},
	methods: {
		getShare: function(id) {
			console.log('>>>>>>>>>>>>>>>>>>>>>>>>>>>>拿到的id是' + id);
			let self = this;
			console.log('>>>>>>>>>>>>>>>>>进入函数');
			uni.request({
				url: BASE_API_URL + '/shares/' + id,
				method: 'GET',
				header: {
					'content-type': 'application/json',
					'X-Token': uni.getStorageSync('token').token
				},
				success: res => {
					console.log(res.data.data.data);
					self.share = res.data.data.data.share;
					self.wxNickanme = res.data.data.data.wxNickanme
					console.log('>>>>>>>>>>>>>>>..' + res.data.data.data.wxNickanme);
				}
			});
		},
		exchange: function() {
			uni.request({
				url: BASE_API_URL + '/shares/exchange',
				method: 'POST',
				data: {
					userId: uni.getStorageSync('user').id,
					shareId: this.share.id
				},
				header: {
					'content-type': 'application/json',
					'X-Token': uni.getStorageSync('token').token
				},
				success: res => {
						uni.showToast({
							title: '兑换成功',
							duration: 2000
						});
						uni.request({
							url: BASE_API_URL + /users/ + uni.getStorageSync('user').id,
							method: 'GET',
							header: {
								'content-type': 'application/json',
								'X-Token': uni.getStorageSync('token').token
							},
							success: res => {
								uni.removeStorageSync('user');
								uni.setStorageSync('user', res.data.data);
								console.log(JSON.stringify(res.data.data))
								uni.switchTab({
									url: './homePage'
								});
							}
						});
					
				}
			});
		}
	}
};
</script>

<style scoped>
.title-box {
	height: 80upx;
	font-size: 16px;
	font-weight: 600;
	padding-left: 40upx;
	padding-top: 40upx;
}
.detail-box {
	height: 80upx;
	font-size: 14px;
	font-weight: 600;
	display: flex;
	align-items: center;
	justify-content: space-around;
	border-bottom: #eeeeee solid 5 upx;
}
.content-box {
	font-size: 12px;
	color: #999999;
	padding-left: 40upx;
}

.bottom-box {
	width: 100%;
	height: 120upx;
	position: absolute;
	bottom: 0;
}
.bottom-inner-box {
	display: flex;
	align-items: center;
	justify-content: flex-end;
}
.red-button {
	position: absolute;
	right: 0;
	bottom: 0;
	background-color: #eb544d;
	color: #ffffff;
	width: 180upx;
}
</style>
