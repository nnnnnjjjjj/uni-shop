<template>
	<view>
		<block v-for="(goods, i) in goodsList" :key="i">
			<view class="goods-item">
			
				<view class="goods-item-left">
					<image :src="goods.goods_small_logo" class="goods-pic">
					</image>
				</view>
				
				<view class="goods-item-right">
					<view class="goods-name">{{goods.goods_name}}</view>
					<view class="goods-info-box">
						<view class="goods-price">
							￥{{goods.goods_price}}
						</view>
					</view>
				</view>
			</view>
		</block>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				queryObj: {
					query: '',
					cid: '',
					pagenum: 1,
					pageSize: 10
				},
				goodsList: [],
				total: 0
			}
		},
		onLoad(options) {
			this.queryObj.query = options.query || ''
			this.queryObj.cid = options.cid || ''
			
			this.getGoodsList()
		},
		methods: {
			async getGoodsList() {
				const {data: res} = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
				console.log(res)
				if (res.meta.status !== 200) return uni.$showMsg()
				
				this.goodsList = res.message.goods
				this.total = res.message.total 
			}
		}
	}
</script>

<style lang="scss">
	.goods-item {
		display: flex;
		padding: 10px 5px;
		border-bottom: 1px solid #f0f0f0;
		
		.goods-item-left {
			.goods-pic {
				width: 100px;
				height: 100px;
				display: block;
			}
		}
		
		.goods-item-right {
			display: flex;
			flex-direction: column;
			justify-content: space-between;
			
			.goods-name {
				font-size: 13px;
			}
			
			.goods-info-box {
				.goods-price {
					color: #C00000;
					font-size: 16px;
				}
			}
		}
	}
</style>
