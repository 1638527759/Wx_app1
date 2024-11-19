<template>
	<view class="my-search-container" :style="{'background-color': bgcolor}" @click="searchBoxHandler">
		<!-- 使用view之间模拟inpu的输入 -->
		<view class="mysearch-box" :style="{'border-radius': radius + 'px'}">
			<uni-icons type="search" size="17"></uni-icons>
			<text class="plaveholder">搜索</text>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			// 背景颜色
			bgcolor: {
				type: String,
				default: "#0055ff",
				// default: "pink"
			},
			// 圆角尺寸
			radius: {
				type: Number,
				default: 18
			}
		},
		name:"my-search",
		data() {
			return {
				
			};
		},
		
		methods: {
			searchBoxHandler(){
				console.log("11111")
				this.$emit("click")
			}
		}
	}
</script>

<style lang="scss">
.my-search-container{
	height: 50px;
	padding: 0 10px;
	display: flex;
	align-items: center;
}
.mysearch-box{
	height: 36px;
	background-color: #ffffff;
	width: 100%;
	display: flex;
	align-items: center;
	justify-content: center;
	
	.plaveholder{
		font-size: 15px;
		margin-left: 5px;
	}
}
</style>