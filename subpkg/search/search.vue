<template>
	<view>
		<view class="search-box">
			<uni-search-bar :radius="100" cancelButton="none" @confirm="search" @input="input"></uni-search-bar>
		</view>
		<!-- 搜索建议 -->
		<view class="sugg-list" v-if="searchResults.length !== 0">
			<view class="sugg-item" @click="gotoDetail(item)" v-for="(item,index) in searchResults.goods" :key="index">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="forward" size="30"></uni-icons>
			</view>
		</view>

		<view class="history-box" v-else>
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons @click="clean" type="trash" size="30"></uni-icons>
			</view>
			<view class="history-list">
				<uni-tag @click="gotoGoodsList" :text="item" v-for="(item,index) in histories" :key="index" ></uni-tag>
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
				searchResults: [],
				historyList: []
			};
		},
		computed: {
			histories() {
				return [...this.historyList.reverse()]

			}
		},
		onLoad(){
		 this.historyList = JSON.parse(uni.getStorageSync('kw') || '{}')	
		},
		methods: {
			input(e) {
				//防抖
				clearTimeout(this.timer)
				this.timer = setTimeout(() => {
					this.kw = e
					this.getSearchList()
				}, 500)
			},
			async getSearchList() {
				if (this.kw.length === 0) {
					this.searchResults = []
					return
				}
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/goods/search', {
					query: this.kw
				})
				if (res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message


				this.saveSearchHistory()
			},
			gotoDetail(item) {
				uni.navigateTo({
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
				})
			},
			saveSearchHistory() {
				const set = new Set(this.historyList)
				set.delete(this.kw)
				set.add(this.kw)
				this.historyList = Array.from(set)
				uni.setStorageSync('kw', JSON.stringify(this.historyList))
			},
			clean(){
				this.historyList = []
				uni.setStorageSync('kw', '[]')
			},
			gotoGoodsList(kw){
				uni.navigateTo({
					url:'/subpkg/goods_list/goods_list?query=' + kw
				})
			}
		}
	}
</script>

<style lang="scss">
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

	.history-box {
		padding: 0 5px;

		.history-title {
			display: flex;
			justify-content: space-between;
			align-items: center;
			font-size: 13px;
			height: 40px;
		}

		.history-list {
			display: flex;
			flex-wrap: wrap;

			.uni-tag {
				margin-top: 5px;
				margin-right: 5px;
			}
		}
	}
</style>
