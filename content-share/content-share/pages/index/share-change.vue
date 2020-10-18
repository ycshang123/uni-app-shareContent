<template>
	<view>
		<view class="title-box">{{share.title}}</view>
		<view class="content-box">{{share.downloadUrl}}复制这段后打开百度网盘手机App，操作更方便哦</view>
		<button class="copy-button" @click="paste()">复制下载地址</button>
	</view>
	
</template>

<script>
	import { request } from '@/utils/request';
	import { BASE_API_URL } from '@/utils/api';
	export default {
		data() {
			return {
				share:null,
				wxNickanme:''
				
			}
		},
		onLoad(option){
			let id = option.id;
			this.getShare(id)
		},
		methods: {
			paste(){
				uni.setClipboardData({
					data:this.share.downloadUrl,
					success:function(){
						uni.showToast({
							title:'下载地址已复制'
						})
						
					}
				})
			},
			getShare:function(id){
				console.log(">>>>>>>>>>>>>>>>>>>>>>>>... "+id)
				let self = this;
				console.log(">>>>>>>>>>>>>>>>>进入函数")
				uni.request({ 
					url: BASE_API_URL+'/shares/'+id,
					method: 'GET',
					header: {     
						'content-type': 'application/json',
						'X-Token': uni.getStorageSync('token').token
					},
					success: res => { 
						console.log(res.data.data.data)
						self.share = res.data.data.data.share;
						console.log('>>>>>>>>>>>>>>>..' + res.data.data.data.share);
					}
				});
				
			}
			
		}
	}
</script>

<style scoped>
	.title-box{
		height: 60upx;
		font-size: 16px;
		font-weight: 600;
		padding: 15upx;
	}
	.content-box{
		height: 80upx;
		font-size: 12px;
		color: #999999;
		padding: 10upx;
	}
	.copy-button{
		height: 100upx;
		background-color: #45B901;
		color: #ffffff;
	}
</style>
