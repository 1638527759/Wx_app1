<template>
	<view>
		<view class="search-box">
			<!-- 使用uni.ui提供的搜索组件 -->
		<uni-search-bar @input="input" :radius="100" cancel-button="none"></uni-search-bar>
		</view>
		
		<!-- 搜索建议列表 -->
		<view class="sugg-list" v-if="searchResults.length !== 0">
			<view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>
		
		<!-- 搜索历史 -->
		<view class="history-box" v-else>
			<!-- 标题区域 -->
			<view class="history-title">
				<text>搜索历史</text>
				<!-- 清除历史 -->
				<uni-icons type="trash" size="17" @click="clean"></uni-icons>
			</view>
			<!-- 列表区域 -->
			<view class="history-list">
				<uni-tag inverted="true" type="primary" :text="item" v-for="(item, i) in histories" :key="i" @click="gotoGoodsList(item)">
					
				</uni-tag>
			</view>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 延时器timer
				timer: null,
				// 搜索关键词
				kw: "",
				// 搜索结果列表
				searchResults: [],
				// 搜索关键词历史
				historyList: [],
				
			};
		},
		
		onload() {
			this.historyList = JSON.parse(uni.getStorageSync("kw") || "[]")
		},
		
		methods: {
			input(e){
				// 	清除timer对应的延时器
				clearTimeout(this.timer)
				
				// 启动一个延时器
				this.timer = setTimeout(() => {
					this.kw = e
					// 根据搜索关键词，搜索商品建议列表
					this.getSearchList()
				}, 500)
			},
			
			// 根据搜索关键词，搜索商品建议列表
			async getSearchList() {
				// 判断关键词是否为空
				if (this.kw.length === 0) {
					this.searchResults = []
					return
				}
				else {
					const {data: res} = await uni.$http.get("/api/public/v1/goods/qsearch", {query: this.kw})
					if (res.meta.status !== 200){
						return uni.$showMsg()
					}
					this.searchResults = res.message
					
					this.saveSearchHistory()
				}
			},
				
			// 记录搜索历史
			saveSearchHistory() {
				// this.historyList.push(this.kw)
				
				// 将数组转化成Set对象
				const set = new Set(this.historyList)
				// 调用Set对象的delete方法，移除对应的元素
				set.delete(this.kw)
				// 调用Set对象的delete方法，向Set中添加元素
				set.add(this.kw)
				// 将Set对象转化为Array数组
				this.historyList = Array.from(set)
				// 历史记录最新在前
				// this.historyList = [...this.historyList].reverse()
				// 将搜索历史持久化存储到本地
				uni.setStorageSync("kw", JSON.stringify((this.historyList)))
			},
			
			// 清空搜索历史
			clean() {
				this.historyList = []
				uni.setStorageSync("kw", "[]")
			},
			
			// 跳转至详情页
			gotoDetail(item) {
				uni.navigateTo({
					url: "/subpkg/goods_detail/goods_detail?goods_id=" + item.goods_id
				})
			},
			
			gotoGoodsList(kw) {
				uni.navigateTo({
					url: "/subpkg/goods_list/goods_list?query=" + kw
				})
			}
			
		},
		
		computed: {
			histories() {
				return [...this.historyList].reverse()
			}
		},
		
	}
</script>

<style lang="scss">
.uni-searchbar {
	display: flex;
	flex-direction: row;
	position: relative;
	padding: 16px;
	background-color: #0055ff;
}
// 搜索区域
.search-box {
	position: sticky;
	top: 0;
	z-index: 999;
}
// 搜索列表
.sugg-list {
	padding: 0 5px;
	
	.sugg-item {
		font-size: 12px;
		padding: 13px 0;
		border-bottom: 1px solid #efefef;
		display: flex;
		align-items: center;
		justify-content: space-between;
		
		.goods-name {
			// 文字不允许换行
			white-space: nowrap;
			// 溢出部分隐藏
			overflow: hidden;
			// 文本溢出后用...代替
			text-overflow: ellipsis;
			margin-right: 3px;
		}
	}
}

// 搜索历史
.history-box {
	padding: 0 5px;
	
	.history-title {
		display: flex;
		justify-content: space-between;
		height: 40px;
		align-items: center;
		font-size: 13px;
		border-bottom: 1px solid #efefef;
	}
	
	.history-list {
		display: flex;
		flex-wrap: wrap;
		
		.uni-tag {
			margin-top: 5px;
			margin-right:5px;
			padding: 6px 10px;
		}
	}
}
</style>
