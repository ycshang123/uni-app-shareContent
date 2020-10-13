<template>
	<view>
		<view class="out-box">
			<view class="center-box" v-if="!user">
				<view class="avatar"><image src="https://wx1.sinaimg.cn/mw690/00669kenly1gjlsfg6juzj31jk11dtft.jpg" class="avatar" /></view>
				<view>登录享受技术之旅吧</view>
				<button class="btn-small" @click="login()">微信登录</button>
				<!-- <button class="btn-small" open-type="getUserInfo" @getuserinfo="bindGetUserInfo">微信登录</button> -->
			</view>
			<view class="center-box" v-else>
				<view class="avatar"><image :src="user.avatarUrl" class="avatar" /></view>
				<view>{{ user.wxNickname }}</view>
				<view>积分:{{ user.bonus }}</view>
				<button class="btn-small" @click="logout">退出</button>
			</view>
		</view>
	</view>
</template>
<script>
import { request } from '@/utils/request';
import { LOGIN_URL } from '@/utils/api';
export default {
	data() {
		return {
			user: {}
		};
	},
	onLoad() {
		if (uni.getStorageSync('user')) {
			this.user = uni.getStorageSync('user');
		}
	},
	methods: {
		bindGetUserInfo(e) {
			let platform = uni.getSystemInfoSync().platform;
			//微信开发者工具调试适用
			if (platform === 'devtools') {
				//此处e.mp.detail事件适用于mini program小程序
				if (e.mp.detail.rawData) {
					//用户按了允许授权按钮
					this.wxLogin(e);
				} else {
					//用户按了拒绝按钮
					uni.showToast({
						title: '授权失败'
					});
				}
			}
		},
		wxLogin(e) {
			let self = this;
			let userInfo = e.mp.detail.userInfo;
			wx.login({
				success(res) {
					if (res.code) {
						console.log('>>>>>>>>>>>>>>>>>>>' + res.code),
							uni.request({
								url: 'http://10.40.157.176:8040/users/wxLogin',
								method: 'POST',
								data: {
									code: res.code,
									wxNickname: userInfo.nickName,
									avatarUrl: userInfo.avatarUrl
								},
								header: {
									'Content-Type': 'application/json'
								},
								success: res => {
									console.log('进入回调函数++++++++++++++++++++++++++++++');
									uni.showToast({
										title: '登录成功',
										duration: 1000
									});
									console.log(res.data.user);
									(self.user = res.data.user), (self.user = res.data.user), uni.setStorageSync('user', res.data.user);
								}
							});
					} else {
						console.log('登陆失败' + res.errMsg);
					}
				}
			});
		},
		login() {
			let self = this;
			uni.login({
				provider: 'weixin',
				success: function(loginRes) {
					console.log('>>>>>>>>>>>>>>>>>>点击登录');
					console.log(loginRes.authResult);
					uni.getUserInfo({
						provider: 'weixin',
						success: infoRes => {
							uni.request({
								url: 'http://10.40.157.176:8040/users/login',
								method: 'POST',
								data: {
									wxId: loginRes.authResult.openid,
									wxNickname: infoRes.userInfo.nickName,
									avatarUrl: infoRes.userInfo.avatarUrl
								},
								header: {
									'Content-Type': 'application/json'
								},
								success: res => {
									console.log('>>>>>>>>>>>>>>>>>>>>>>进入回调函数');
									console.log(res.data.user);
									(self.user = res.data.user), (self.user = res.data.user), uni.setStorageSync('user', res.data.user);
								}
							});
						}
					});
				}
			});
		},
		logout() {
			console.log(this.user);
			this.user = null;
			uni.removeStorageSync('user');
		}
	}
};
</script>

<style scoped>
.out-box {
	padding: 20upx;
	height: 360upx;
}
.center-box {
	display: flex;
	flex-direction: column;
	justify-content: space-around;
	align-items: center;
	height: 380upx;
}
.avatar {
	width: 200upx;
	height: 200upx;
	border-radius: 50%;
	background-color: #007aff;
}
.btn-small {
	height: 50upx;
	width: 200upx;
	background-color: #45b901;
	color: #ffffff;
	font-size: 12px;
	border: none;
}
.item-row {
	display: flex;
	justify-content: space-between;
	padding: 12upx;
	height: 48upx;
	padding: 16upx;
	font-size: 14px;
	border-bottom: 1px solid #dfdfdf;
}
</style>
