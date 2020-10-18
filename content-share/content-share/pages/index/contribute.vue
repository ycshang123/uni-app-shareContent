<template>
	<view>
		<view class="tip">说明：投稿审核通过后会有积分奖励；资源被下载会有积分奖励；资源被下载会有积分奖励；提交的资源不得包含 广告、侵权信息；百度盘地址建议有密码。</view>
		<radio-group>
			<view class="item-row" style="display: flex; justify-content: space-between;align-items: center;">
				<view class="subtitle">原创</view>
				<radio value="原创" checked="checked" @click="getValue()" />
			</view>
			<view class="item-row" style="display: flex; justify-content: space-between;align-items: center;">
				<view class="subtitle">转载</view>
				<radio value="转载" @click="getValue()" />
			</view>
		</radio-group>
		<view class="item-row">
			<view class="subtitle">标题</view>
			<input type="text" placeholder="请输入标题" class="input-style" v-model="shareDto.title" />
		</view>
		<view class="item-row">
			<view class="subtitle">作者</view>
			<input type="text" placeholder="请输入作者" class="input-style" v-model="shareDto.author" />
		</view>
		<view class="item-row">
			<view class="subtitle">价格</view>
			<input type="number" placeholder="请输入价格" class="input-style" v-model="shareDto.price" />
		</view>
		<view class="item-row">
			<view class="subtitle">简介</view>
			<input type="text" placeholder="介绍一下技术干货吧" class="input-style" v-model="shareDto.summary" />
		</view>
		<view class="item-row">
			<view class="subtitle">下载地址</view>
			<input type="text" placeholder="请输入下载地址" class="input-style" v-model="shareDto.downloadUrl" />
		</view>
		<button class="btn-big" @click="contribute">提交</button>
	</view>
</template>

<script>
import { BASE_API_URL } from '@/utils/api';
export default {
	data() {
		return {
			radioCheck: true,
			shareDto: {
				userId: uni.getStorageSync('user').id,
				isOriginal: 1,
				title: '',
				author: '',
				price: '',
				summary: '',
				downloadUrl: ''
			}
		};
	},
	onLoad() {
	},
	methods: {
		getValue: function(e) {
			this.radioCheck = !this.radioCheck;
			if (this.radioCheck) {
				this.shareDto.isOriginal = 1;
			} else {
				this.shareDto.isOriginal = 0;
			}
			console.log('单选按钮动态值：' + this.radioCheck);
			console.log(this.shareDto.isOriginal);
		},  
		contribute: function() {
			console.log(this.shareDto);
			uni.request({
				url: BASE_API_URL + '/shares/contribute',
				data: this.shareDto,
				method: 'POST',
				header: {
					'content-type': 'application/json',
					'X-Token': uni.getStorageSync('token').token
				},
				success: res => {
					uni.showToast({
						title: '投稿成功',
						duration: 2000
					});
				}
			});
		}
	}
};
</script>

<style scoped>
.tip {
	padding: 20upx;
	height: 100upx;
	font-size: 12px;
	border-bottom: 1px solid #dfdfdf;
}
.item-row {
	display: flex;
	padding: 15upx;
	height: 48upx;
	border-bottom: 1px solid #dfdfdf;
}
.flex-around {
	display: flex;
	align-items: center;
	justify-content: space-around;
}
.subtitle {
	font-size: 14px;
	width: 25%;
}
.input-style {
	width: 75%;
}
.btn-big {
	background-color: #fe4543;
	color: #ffffff;
	height: 50px;
}
</style>
