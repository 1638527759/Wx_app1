<template>
	<view>
		<!-- 搜索区域 -->
		<view class="search-box">
			<my-search @click="gotoSearch"></my-search>
		</view>
		
		
		<!-- 轮播图区域 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" circular="true">
			<swiper-item v-for="(item, i) in swiperList" :key="i">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
			<swiper-item>
				<view class="swiper-item"></view>
			</swiper-item>
		</swiper>
		
		<!-- 分类导航区域 -->
		<view class="nav-list">
			<view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClickHandler(item)">
				<image :src="item.image_src" class="nav-img"></image>
			</view>
		</view>
		
		<!-- 楼层区域 -->
		<view class="floor-list">
			<!-- 每一个楼层的item项 -->
			<view class="floor-item" v-for="(item, i) in floorList" :key="i">
				<!-- 楼层标题 -->
				<image :src="item.floor_title.image_src" class="floor-title"></image>
				<!-- 楼层的图片 -->
				<view class="floor-img-box">
					<!-- 左边大图的盒子 -->
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src" mode="widthFix" :style="{width: item.product_list[0].image_width + 'rpx'}"></image>
					</navigator>

					<!-- 右边四格图片盒子 -->
					<view class="right-img-box">
						<navigator class="right-img-item" v-for="(item2, i2) in item.product_list"  :key="i2" :url="item2.url">
							<image :src="item2.image_src" mode="widthFix" v-if="i2 !== 0" :style="{width: item2.image_width + 'rpx'}"></image>		
						</navigator>
					</view>
				</view>
			</view>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 轮播图的数据列表
				swiperList: [],
				// 分类导航的数据列表
				navList: [],
				// 楼层的数据列表
				floorList: []
			};
		},
		
		onLoad() {
			// 调用方法获取轮播图的数据
			this.getSwiperList()
			// 调用方法获取导航栏的数据
			this.getNavList()
			// 获取楼层数据
			this.getfloorList()
		},
		
		methods: {
			// 获取轮播图数据
			async getSwiperList() {
				const {data: res} = await uni.$http.get("/api/public/v1/home/swiperdata")
				// console.log(res)
				// console.log("！！！！！！！！")
				// 请求失败
				if (res.meta.status !== 200){
					return uni.$showMsg()
				}
				// if (res.meta.status !== 200) {
				// 	return uni.showToast({
				// 		title: "数据请求失败！",
				// 		duration: 1500,
				// 		icon: "none"
				// 	})
				// }
					
				// 请求成功
				this.swiperList = res.message
				// uni.$showMsg("轮播图请求成功!")
			},
			
			// 获取导航栏数据
			async getNavList(){
				const {data: res} = await uni.$http.get("/api/public/v1/home/catitems")
				// 请求失败
				if (res.meta.status !== 200){
					return uni.$showMsg()
				}
				// 请求成功
				this.navList = res.message
				// uni.$showMsg("导航栏请求成功!")
			},
			
			// 跳转到分类
			navClickHandler(item){
				if (item.name === "分类"){
					uni.switchTab({
						url: "/pages/cate/cate"
					})
				}
			},
			
			// 获取首页楼层数据
			async getfloorList(){
				const {data: res} = await uni.$http.get("/api/public/v1/home/floordata")
				if(res.meta.status !== 200){
					return uni.$showMsg()
				}
				
				// 对数据进行处理
				res.message.forEach(floor => {
					floor.product_list.forEach(prod => {
						prod.url = "/subpkg/goods_list/goods_list?" + prod.navigator_url.split("?")[1]
						})
				})
				// 请求成功
				this.floorList = res.message
			},
			
			// 跳转至搜索页面
			gotoSearch(){
				uni.navigateTo({
					url: "/subpkg/search/search"
				})
			}
		}
	}
</script>

<style lang="scss">
// 轮播图
swiper{
	height: 330rpx;
	.swiper-item,
	image{
		width: 100%;
		height: 100%;
	}
}
// 导航栏
.nav-list{
		display: flex;
		justify-content: space-around;
		margin: 15px 0;
		
		.nav-img{
			width: 128rpx;
			height: 140rpx;
		}
}
// 楼层标题
.floor-title{
	height: 60rpx;
	width: 100%;
	display: flex;
}
// 楼层右侧小图
.right-img-box{
	display: flex;
	flex-wrap: wrap;
	justify-content: space-around; 
}
// 楼层图片
.floor-img-box{
	display: flex;
	padding-left: 10rpx;
}
// 搜索区域
.search-box{
	position: sticky;
	top: 0;
	z-index: 999;
}
</style>
