<template>
	<view>
		<my-search @click="gotoSearch"></my-search>
		<view class="scroll-view-container">
			<!-- 左侧滑动区 -->
			<scroll-view class="left-scroll-view" scroll-y="true" :style="{height: wh + 'px'}">
				<block v-for="(item,index) in cateList" :key="index">
					<view class="left-scroll-view-item" @click="activeChange(index)"
						:class="index === active ? 'active' : ''">
						{{item.cat_name}}
					</view>
				</block>

			</scroll-view>
			<scroll-view class="right-scroll-view" scroll-y="true" :style="{height: wh + 'px'}" :scroll-top="scrollTop">
				<view class="cate-lv2" v-for="(item2,i2) in cateLevel2" :key="i2">
					<view class="cate-lv2-title">
						/ {{item2.cat_name}} /
					</view>
					<!-- 三级分类 -->
					<view class="cate-lv3-list">
						<view class="cate-lv3-item" v-for="(item3,i3) in item2.children" :key="i3"
							@click="gotoGoodsList(item3)">
							<image :src="item3.cat_icon" alt="">
								<text>{{item3.cat_name}}</text>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	import badgeMix from '@/mixins/tabbar-badge.js'
	export default {
		mixins: [badgeMix],
		data() {
			return {
				//当前设备可用高度
				wh: 0,
				active: 0,
				scrollTop: 0,
				cateList: [],
				cateLevel2: []
			};
		},
		onLoad() {
			//获取当前设备可用高度
			const sysInfo = uni.getSystemInfoSync()
			console.log(sysInfo)
			this.wh = sysInfo.windowHeight - 50


			//获取左侧分类数据
			this.getCateList()
		},
		methods: {
			activeChange(index) {
				this.active = index
				//重新为二级分类赋值
				this.scrollTop = this.scrollTop === 0 ? 1 : 0
				this.cateLevel2 = this.cateList[index].children
			},
			async getCateList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/categories')
				console.log(res)
				if (res.meta.status !== 200) return uni.$showMsg()
				//一级分类
				this.cateList = res.message
				//二级分类
				this.cateLevel2 = res.message[0].children

				uni.$showMsg('数据请求成功')
			},
			gotoGoodsList(item3) {
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
				})
			},
			gotoSearch() {
				uni.navigateTo({
					url: '../../subpkg/search/search'
				})
			}
		}
	}
</script>

<style lang="scss">
	.scroll-view-container {
		display: flex;

		.left-scroll-view {
			width: 120px;

			.left-scroll-view-item {
				background-color: #f7f7f7;
				line-height: 60px;
				text-align: center;
				font-size: 12px;

				&.active {
					background-color: #fff;
					position: relative;

					&::before {
						content: ' ';
						display: block;
						width: 3px;
						height: 30px;
						background-color: #c00000;
						position: absolute;
						top: 50%;
						left: 0;
						transform: translateY(-50%);
					}
				}
			}
		}

		.cate-lv2-title {
			font-size: 12px;
			font-weight: bold;
			text-align: center;
		}

		.cate-lv3-list {
			display: flex;
			flex-wrap: wrap;

			.cate-lv3-item {
				width: 33.33%;
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: center;
				margin-bottom: 10px;

				image {
					width: 60px;
					height: 60px;
				}

				text {
					font-size: 12px;
				}
			}
		}
	}
</style>
