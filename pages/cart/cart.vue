<template>
	<view class="cart-container" v-if="cart.length !== 0">
		<!-- 收货地址组件 -->
		<my-address></my-address>
		<view class="cart-title">
			<uni-icons type="shop" size="18"></uni-icons>
			<text class="cart-title-text">购物车</text>
		</view>


		<uni-swipe-action>
			<block v-for="(goods,i) in cart" :key="i">
				<uni-swipe-action-item :right-options="options" @click="delGoods(goods)">
					<my-goods @num-change="numberChangeHandler" @radios-change="radioChangeHandler" :goods="goods"
						:showNum="true" :showRadio="true"></my-goods>
				</uni-swipe-action-item>

			</block>
		</uni-swipe-action>


		<!-- 计算 -->
		<my-settle></my-settle>
	</view>
	
	
	<view class="empty-cart" v-else>
		<image src="../../static/cart.png" class="empty-img"></image>
		<text class="tip-text">空空如也</text>
	</view>
</template>

<script>
	import badgeMix from '@/mixins/tabbar-badge.js'
	import {
		mapState,
		mapMutations
	} from 'vuex'
	export default {
		mixins: [badgeMix],
		data() {
			return {
				options: [
					// 	{
					// 	text: '取消',
					// 	style: {
					// 		backgroundColor: '#007aff'
					// 	}
					// }, 
					{
						text: '删除',
						style: {
							backgroundColor: '#dd524d'
						}
					},
				]
			};
		},
		computed: {
			...mapState('m_cart', ['cart'])
		},
		methods: {
			...mapMutations('m_cart', ['updateGoodsState', 'updateGoodsCount', 'removeGoods']),
			radioChangeHandler(e) {
				// console.log(e)
				this.updateGoodsState(e)
			},
			numberChangeHandler(e) {
				// console.log(e)
				this.updateGoodsCount(e)
			},
			delGoods(goods) {
				this.removeGoods(goods.goods_id)
			}
		}

	}
</script>

<style lang="scss">
	.cart-container {
		padding-bottom: 50px;
	}

	.cart-title {
		height: 40px;
		display: flex;
		align-items: center;
		padding-left: 5px;
		border-bottom: 1px solid #efefef;

		.cart-title-text {
			font-size: 14px;
			margin-left: 10px;

		}
	}
	.empty-cart{
		// background-color: #f6f6f6;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		padding-top: 150px;
		.empty-img{
			width: 90px;
			height: 90px;
		}
		.tip-text{
			font-size: 12px;
			color: gray;
			margin-top: 15px;
		}
	}
</style>
