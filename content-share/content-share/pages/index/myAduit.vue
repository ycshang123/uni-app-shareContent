<template>
	<view>
		<view class="item-row" v-for="(item, index) in items" :key="index">
			<view class="avatar">
				<image :src="item.cover" class="avatar" />
				<view class="avatar-lable" v-if="item.isOriginal == true" style="background-color: #EB544D;color:#F1F1F1;font-size: 14px;">原创</view>
				<view class="avatar-lable" v-else style="background-color: #4CD964;color:#F1F1F1;font-size: 14px;">转载</view>
			</view>
			<view class="content">
				<view class="share-title">{{ item.title }}</view>
				<view class="content-title">{{ item.summary.substring(0, 25) }}……</view>
			</view>
			<view class="score">
				<view class="content-title" @click="goDetail(item.downloadUrl, item.id)" v-if="item.auditStatus == 'PASS'">审核通过</view>
				<view class="content-title" @click="checkStatus" v-else>暂未审核</view>
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
			items: []
		};
	},
	onShow() {
		this.getShare();
	},
	methods: {
		getShare: function() {
			uni.request({
				url: BASE_API_URL + '/shares/auditList/' + uni.getStorageSync('user').id,
				method: 'GET',
				header: {
					'content-type': 'application/json'
				},
				success: res => {
					console.log(res.data.data.data);
					this.items = res.data.data.data;
				}
			});
		},
		goDetail(downloadUrl, id) {
			uni.navigateTo({
				url: './share-change?id=' + id
			});
		},
		checkStatus(){
			uni.showModal({
				content: '资源暂未审核'
			});
		}
	}
};
</script>

<style>
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
	width: 18%;
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
