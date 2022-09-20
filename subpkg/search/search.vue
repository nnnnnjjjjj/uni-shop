<template>
	<view>
		<view class="search-box">
			<uni-search-bar @input="input" :radius="100" cancelButton="none"></uni-search-bar>
		</view>
		
		<!-- 搜索建议列表 -->
		<view class="sugg-list">
			<view class="sugg-item" v-for="(item, index) in searchResults" :key="i" @click="gotoDetail(item)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>
		
		<!-- 搜索历史 -->
		<view class="history-box">
			<!-- 标题区域 -->
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17"></uni-icons>
			</view>
			
			<!-- 列表区域 -->
			<view class="history-list">
				<uni-tag text="标签" v-for="(item, i) in historyList" :key="i"></uni-tag>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				timer: null,
				kw: '',
				searchResults:[],
				historyList:['a', 'app', 'apple'],
			};
		},
		methods:{
			input(e) {
				
				clearTimeout(this.timer)
				
				this.timer = setTimeout(() => {
					this.kw = e
					this.getSearchList()
				}, 500)
				
			},
			async getSearchList() {
				// 判断搜索关键字是否为空
				if (this.kw.length === 0) {
					this.searchResults = []
					return
				}
				
				const {data: res} = await uni.$http.get('/api/public/v1/goods/qsearch', {query: this.kw})
				if (res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message
			},
			
			gotoDetail(item) {
				console.log(item)
				uni.navigateTo({
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
				})
			}
		}
	}
</script>

<style lang="scss">
	.search-box {
		position: sticky;
		top: 0;
		z-index: 999;
	}
	
	.sugg-list {
		padding: 0 5px;
		.sugg-item {
			display: flex;
			align-items: center;
			justify-content: space-between;
			font-size: 12px;
			padding: 13px 0;
			border-bottom: 1px solid #efefef;
			
			.goods-name {
				white-space: nowrap;
				overflow: hidden;
				text-overflow: ellipsis;
			}
		}
	}
</style>
