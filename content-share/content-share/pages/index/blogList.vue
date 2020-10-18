<template>
	<view>
		<view class="item-row" v-for="(item, index) in items" :key="index">
			<view class="item-left">
				<image src="https://wx2.sinaimg.cn/thumb150/006eXSq0gy1gjo60xaogoj300w00w0gt.jpg" class="lable" v-if="item.event == 'SIGN_IN'"/>
				<image src
				="https://wx3.sinaimg.cn/mw690/006eXSq0gy1gjo60x5klvj300w00w0rh.jpg" class="lable" v-if ="item.event =='EXCHANGE'"/> 
				<image
				src="https://wx3.sinaimg.cn/mw690/006eXSq0gy1gjtinfbxyvj300w00w0i1.jpg" class="lable" v-if ="item.event == 'CONTRIBUTE'"/>
				<span style="font-size: 14px;">{{ item.createTime.substring(0,22) }}-{{ item.description }}</span>
			</view>
			<view class="item-right">
				<span>{{ item.value }}</span>
			</view>
		</view>
	</view>
</template>

<script>
import { BASE_API_URL } from '@/utils/api';
export default {
	data() {
		return {
			items: []
		};
	},
	onShow() {
		this.getBlog();
	},
	methods: {
		getBlog: function() {
			console.log(uni.getStorageSync('user').id);
			uni.request({
				url: BASE_API_URL + '/api/list/' + uni.getStorageSync('user').id,
				method: 'GET',
				header: {
					'content-type': 'application/json'
				},
				success: res => {
					console.log(res.data);
					this.items = res.data.data;
				}
			});
		}
	}
};
</script>

<style scoped>
.item-row {
	margin: 20upx;
	display: flex;
	justify-content: space-between;
	align-items: center;
	height: 80upx;
	border-bottom: 0.5px solid #848484;
}
.item-left {
	width: 70%;
	display: flex;
	justify-content: space-around;
	align-items: center;
}
.item.right {
	width: 15%;
}
.lable {
	width: 42upx;
	height: 42upx;
}
</style>
