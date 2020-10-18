<template>
	<view>
		<view class="out-box">
			<view class="center-box" v-if="!user">
				<view class="avatar"><image src="https://wx1.sinaimg.cn/mw690/00669kenly1gjlsfg6juzj31jk11dtft.jpg" class="avatar" /></view>
				<view>登录享受技术之旅吧</view>
				<!-- <button class="btn-small" @click="login()">微信登录</button> -->
				<button class="btn-small" @click="applogin()" open-type="getUserInfo" @getuserinfo="bindGetUserInfo">微信登录</button>
			</view>
			<view class="center-box" v-else>
				<view class="avatar"><image :src="user.avatarUrl" class="avatar" /></view>
				<view>{{ user.wxNickname }}</view>
				<view>积分:{{ user.bonus }}</view>
				<view style="display: flex; justify-content: space-around;width: 100%;">
					<button class="btn-small" @click="userSignIn">签到</button>
					<button class="btn-small" @click="logout">退出</button>
				</view>

				<view class="item-row" @click="toMyResource()">
					<view>我的兑换</view>
					<view>></view>
				</view>

				<view class="item-row" @click="toBlogList()">
					<view>积分明细</view>
					<view>></view>
				</view>
				<view class="item-row" @click="toMyAudit()">
					<view>我的投稿</view>
					<view>></view>
				</view>
				<view class="item-row" v-if="user.roles == '管理员'" @click="toNoPass()">
					<view>投稿审核</view>
					<view>></view>
				</view>
			</view>
		</view>
	</view>
</template>
<script>
import { request } from '@/utils/request';
import { LOGIN_URL, BASE_API_URL } from '@/utils/api';
export default {
	data() {
		return {
			user: null
		};
	},
	onLoad() {
		if (uni.getStorageSync('user')) {
			this.user = uni.getStorageSync('user');
		}
	},
	onShow() {
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
					if (res.errMsg == 'login:ok') {
						let loginDTO = {
							loginCode: res.code,
							wxNickname: userInfo.nickName,
							avatarUrl: userInfo.avatarUrl
						};
						self.userLogin(loginDTO);
					} else {
						console.log('登录失败' + res.errMsg);
					}
				}
			});
		},
		applogin() {
			let self = this;
			uni.login({
				provider: 'weixin',
				success: function(loginRes) {
					console.log(loginRes.authResult);
					uni.getUserInfo({
						provider: 'weixin',
						success: infoRes => {
							console.log(infoRes.userInfo);
							//统一的登录请求参数
							let loginDTO = {
								openId: infoRes.userInfo.openId,
								wxNickname: infoRes.userInfo.nickName,
								avatarUrl: infoRes.userInfo.avatarUrl
							};
							self.userLogin(loginDTO);
						}
					});
				}
			});
		},

		userLogins(loginDTO) {
			console.log(loginDTO);
			request(LOGIN_URL, 'POST', loginDTO).then(res => {
				uni.showToast({
					title: '登录成功',
					duration: 1000
				});
				this.user = res.data.user;
				console.log(this.user);
				uni.setStorageSync('user', res.data.user);
				uni.setStorageSync('token', res.data.token);
			});
		},

		userLogin(loginDTO) {
			let self = this;
			uni.request({
				url: BASE_API_URL + '/users/login',
				method: 'POST',
				data: loginDTO,
				header: {
					'Content-Type': 'application/json',
					'X-Token': uni.getStorageSync('token').token
				},
				success: res => {
					console.log(res.data);
					uni.showToast({
						title: '登录成功',
						duration: 1000
					});
					console.log(JSON.stringify(res.data));
					(self.user = res.data.userRespDto), uni.setStorageSync('user', res.data.userRespDto);
					uni.setStorageSync('token', res.data.token);
					console.log('token>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>' + uni.getStorageSync('token').token);
				}
			});
		},
		userSignIn() {
			console.log('》》》》》》》》》》》》》》点击了签到按钮');
			console.log('>>>>>>>>>>>>>>>>>>用户的id为' + uni.getStorageSync('user').id);
			uni.request({
				url: BASE_API_URL + '/users/signIn',
				method: 'POST',
				data: {
					userId: this.user.id
				},
				header: {
					'Content-Type': 'application/json',
					'X-Token': uni.getStorageSync('token').token
				},
				success: res => {
					console.log('签到接口请求成功');
					console.log(res.data.code);
					if (res.data.code == 200) {
						this.user.bonus = this.user.bonus + 20;
						uni.showToast({
							title: '签到成功',
							duration: 1000
						});
					} else if (res.data.code == 201) {
						uni.showModal({
							content: '不能重复签到'
						});
					} else {
						uni.showModal({
							content: '签到已结束'
						});
					}
				}
			});
		},

		logout() {
			console.log(this.user);
			this.user = null;
			uni.removeStorageSync('user');
			uni.removeStorageSync('token');
		},
		toBlogList() {
			uni.navigateTo({
				url: './blogList'
			});
		},
		toMyResource() {
			uni.navigateTo({
				url: './myResource'
			});
		},
		toMyAudit() {
			uni.navigateTo({
				url: './myAduit'
			});
		},
		toNoPass() {
			uni.navigateTo({
				url: './noPass'
			});
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
	height: 800upx;
}
.avatar {
	width: 200upx;
	height: 200upx;
	border-radius: 50%;
	background-color: #007aff;
}
.btn-small {
	height: 60upx;
	width: 160upx;
	background-color: #45b901;
	color: #ffffff;
	font-size: 12px;
	border: none;
}
.item-row {
	display: flex;
	justify-content: space-between;
	width: 100%;
	padding: 12upx;
	height: 48upx;
	padding: 16upx;
	font-size: 14px;
	border-bottom: 1px solid #dfdfdf;
}
</style>
