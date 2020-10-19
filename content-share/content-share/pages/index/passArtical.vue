<template>
	<view>
		<view class="title-box">{{ share.title }}</view>
		<view class="detail-box">
			<view>作者:{{ share.author }}</view>
			<view>发布人:{{ share.author }}</view>
			<view>下载数:{{ share.buyCount }}</view>
		</view>
		<view class="content-box">{{ share.summary }}</view>
		<view style="height: 250upx;width: 100%;"><image :src="share.cover" style="height: 250upx;width: 100%;" /></view>
		<view style="color: #8C8C8C;font-size: 12px;">{{ share.downloadUrl }}</view>
		<view style="height: 3upx;background-color: #C0C0C0;"></view>
		<view class="item-row">
			通过审核
			<switch checked @click="changeStatus()" />
		</view>
		<input type="text" placeholder="请输入拒绝审核原因" class="input-style" v-model="this.shareAuditDto.auditStatusEnum" v-if="!status1" />
		<view class="item-row">
			立刻发布
			<switch checked @click="changeStatus1()" />
		</view>
		<input type="text" placeholder="请输入拒绝发布原因" class="input-style" v-model="this.shareAuditDto.reason" v-if="!status2" />
		<button class="copy-button" @click="getSubmit(share.id)">提交</button>
	</view>
</template>

<script>
import { BASE_API_URL } from '@/utils/api';
export default {
	data() {
		return {
			share: {},
			shareAuditDto: {
				auditStatusEnum: ' ',
				reason: ' ',
				showFlag: true
			},
			status1: true,
			status2: true
		};
	},
	onShow() {
		this.getShare(id);
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
					console.log('>>>>>>>>>>>>>>>..' + res.data.data.data.wxNickname);
				}
			});
		},
		getSubmit: function(id) {
			console.log('>>>>>>>>>>>>>>>>>>>>>>>>>>>>拿到的id是' + this.share.id);
			if (this.status1) {
				(this.shareAuditDto.auditStatusEnum = 'PASS'), (this.shareAuditDto.reason = '发布成功'), (this.shareAuditDto.showFlag = true);
			}
			console.log('>>>>>>>>>>>>>>>>>进入函数');
			uni.request({
				url: BASE_API_URL + '/shares/auditById/' + this.share.id,
				method: 'PUT',
				header: {
					'content-type': 'application/json'
				},
				data: this.shareAuditDto,
				success: res => {
					console.log(res.data);
					uni.showToast({
						title: '审批成功'
					});
					if (this.status1) {
						console.log(uni.getStorageSync('user').bonus);
						uni.setStorageSync('user').bonus = uni.getStorageSync('user').bonus + 50;
					}
				}
			});
		},
		changeStatus() {
			this.status1 = !this.status1;
			this.shareAuditDto.showFlag = false;
		},
		changeStatus1() {
			this.status2 = !this.status2;
			this.shareAuditDto.showFlag = false;
		}
	}
};
</script>

<style scoped>
.input-style {
	height: 60upx;
	border: 0.5px solid #8c8c8c;
}
.title-box {
	height: 60upx;
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
.item-row {
	height: 120upx;
	display: flex;
	align-items: center;
	justify-content: space-between;
}
.copy-button {
	height: 100upx;
	background-color: #dd524d;
	color: #ffffff;
}
</style>
