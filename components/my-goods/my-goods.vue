<template>

	<view class="goods-item">
		<view class="goods-item-left">
			<radio @click="radioClickHandler" :checked="goods.goods_state" color="#c00000" v-if="showRadio"></radio>
			<img :src="goods.goods_small_logo || defaultPic" alt="" class="goods-pic">
		</view>
		<view class="goods-item-right">
			<view class="goods-name">{{goods.goods_name}}</view>
			<view class="goods-info-box">
				<view class="goods-price">￥{{goods.goods_price | tofixed}}</view>
				<uni-number-box @change="numChangeHandler" v-if="showNum" :min="1" :value="goods.goods_count" />
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: "my-goods",
		data() {
			return {
				defaultPic: 'https://assets.halo.autocode.gg/externals/infinite/cms-images/?hash=aW1hZ2VzL2ZpbGUvbmV3cy9FdmVyZ3JlZW4vT3V0Y29tZXNfTU9URHYyLmpwZw%3D%3D'
			};
		},
		props: {
			goods: {
				type: Object,
				default: {}
			},
			showRadio: {
				type: Boolean,
				default: false
			},
			showNum: {
				type: Boolean,
				default: false
			}
		},
		filters: {
			tofixed(num) {
				return Number(num).toFixed(2)
			}
		},
		methods: {
			radioClickHandler() {
				this.$emit('radios-change', {
					goods_id: this.goods.goods_id,
					goods_state: !this.goods.goods_state
				})
			},
			numChangeHandler(val) {
				this.$emit('num-change', {
					goods_id: this.goods.goods_id,
					goods_count: +val
				})
			}
		}
	}
</script>

<style lang="scss">
	.goods-item {
		width: 750rpx;
		box-sizing: border-box;
		padding: 10px 5px;
		display: flex;
		border-bottom: 1px solid #f0f0f0;

		.goods-item-left {
			margin-right: 5px;
			display: flex;
			justify-content: space-between;
			align-items: center;

			.goods-pic {
				width: 100px;
				height: 100px;
				display: block;
			}
		}

		.goods-item-right {
			display: flex;
			flex: 1;
			flex-direction: column;
			justify-content: space-between;

			.goods-name {
				font-size: 13px;
			}

			.goods-info-box {
				display: flex;
				justify-content: space-between;
				align-items: center;

				.goods-price {
					color: #c00000;
					font-size: 16px;
				}
			}
		}
	}
</style>
