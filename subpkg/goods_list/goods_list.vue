<template>
	<view>
		<view v-for="(goods, i) in goodsList" :key="i" @click="gotoDetail(goods)">
			<my-goods :goods="goods"></my-goods>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			goods: {
				type: Object,
				default: {},
			}
		},
		data() {
			return {
				queryObj: {
					query: '',
					cid: '',
					pagenum: 1,
					pageSize: 10
				},
				goodsList: [],
				total: 0,
				isloading: false
			}
		},
		// 触底的事件
		onReachBottom() {
			if (this.queryObj.pagenum * this.queryObj.pageSize >= this.total) return uni.$showMsg()
			
			if (this.isloading) return 
			
			this.queryObj.pagenum += 1
			this.getGoodsList()
		},
		onPullDownRefresh() {
			// 重置关键数据
			this.queryObj.pagenum = 1
			this.total = 0
			this.isloading = false
			this.goodsList = []
			
			this.getGoodsList(() => uni.stopPullDownRefresh())
		},
		onLoad(options) {
			this.queryObj.query = options.query || ''
			this.queryObj.cid = options.cid || ''
			
			this.getGoodsList()
		},
		methods: {
			async getGoodsList(cb) {
				this.isloading = true
				const {data: res} = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
				this.isloading = false
				cb && cb()
				if (res.meta.status !== 200) return uni.$showMsg()
				
				this.goodsList = [...this.goodsList, ...res.message.goods]
				this.total = res.message.total 
			},
			gotoDetail(item) {
				uni.navigateTo({
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
				})
			}
		}
	}
</script>

<style lang="scss">
</style>
