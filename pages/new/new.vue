<template>
	<view class="new">
		<block v-for="(item,index) in list" :key="index">
			<view class="card" @click="goDetail(item)">
				<image :src="item.img_src"></image>
				<text class="card-num-view">{{item.img_num}}P</text>
				<view class="bottm">
					<view class="">
						<text>{{item.title}}</text>
					</view>
					<view @click.stop="share(item)" class="card-share-view"></view>
				</view>
			</view>
			
		</block>
	</view>
</template>

<script>
export default {
	data() {
		return {
			refreshing: false,
			providerList: [],
			list: [],
			fetchPageNum: 1
		};
	},
	onLoad() {
		this.getData();
	},
	onPullDownRefresh() {
			console.log("下拉刷新");
			this.refreshing = true;
			this.getData();
	},
	onReachBottom() {
            console.log("滑动到页面底部")
			this.getData();
	},
	methods:{
		getData(){
			uni.request({
				url: this.$serverUrl + '/api/picture/posts.php?page=' + (this.refreshing ? 1 : this.fetchPageNum) + '&per_page=5',				
				success: (res) => {
					console.log(res);
					if (res.statusCode !== 200) {
							console.log("失败!");
					}else{
						if (this.refreshing && res.data[0].id === this.list[0].id) {
								uni.showToast({
									title: "已经最新",
									icon: "none",
								})
								this.refreshing = false;
								uni.stopPullDownRefresh();
								return;
						}
						if (this.refreshing) {
								this.refreshing = false;
								uni.stopPullDownRefresh()
								this.list = res.data;
								this.fetchPageNum = 2;
						} else {
								this.list = this.list.concat(res.data);
								this.fetchPageNum += 1;
						}
					}
				
				}
			});
		},
		share(e){
			
		},
		goDetail(e){
			uni.navigateTo({
					url:"../detail/detail?data=" + encodeURIComponent(JSON.stringify(e))
				})
		}
		
	}
	
}
</script>

<style>
.new{
	flex: 1;
	width:750upx;
	min-height: 100vh;
	flex-direction: column;
	justify-content: center;
	align-items: center;
}
.card{
	position: relative;
	width: 710upx;
	margin: 20upx 20upx 20upx 20upx;
	border-radius: 10upx;
	overflow: hidden;
	flex-direction: column;
	background-color: #FFFFFF;
}
.card image{
	width:710upx;
	heiht:1000upx;
}
.card-num-view {
	background-color: #FF80AB;
    line-height: 1;
    display: inline-block;
	padding: 3px 6px;
    color: #FFFFFF;
    font-size: 12px;
    text-align: center;
	justify-content: center;
    align-items: center;
	border-radius: 15px;
	position: absolute;
	top: 20upx;
	right: 20upx;
}
.bottm{
	justify-content: center;
	align-items: center;
}


</style>
