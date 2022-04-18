<template>
	<view>
		<view class="address-choose-box" v-if="JSON.stringify(address) === '{}'">
			<button type="primary" @click="chooseAddress" size="mini" class="btnChooseAddress">请选择收货地址+</button>
		</view>

		<view class="address-info-box" v-else @click="chooseAddress">
			<view class="row1">
				<view class="row1-left">
					<view class="username">收货人：{{address.userName}}</view>
				</view>
				<view class="row1-right">
					<view class="phone">电话: {{address.telNumber}}</view>
					<uni-icons type="arrowright"></uni-icons>
				</view>
			</view>
			<view class="row2">
				<view class="row2-left">收货地址:</view>
				<view class="row2-right">{{addstr}}</view>
			</view>
		</view>

		<image mode="widthFix" src="../../static/cart_border@2x.png" class="address-border"></image>
	</view>
</template>

<script>
	import {
		mapState,
		mapMutations,
		mapGetters
	} from 'vuex'
	export default {
		name: "my-address",
		data() {
			return {
				// address: {

				// }
			};
		},
		computed: {
			...mapState('m_user',['address']),
			...mapGetters('m_user',['addstr'])
			
		},
		methods: {
			...mapMutations('m_user',['updateAddress']),
			async chooseAddress() {
				const [err, succ] = await uni.chooseAddress().catch(err => err)
				if (err === null && succ.errMsg === 'chooseAddress:ok') {
					this.updateAddress(succ)
					
				}
				//重新授权，官方似乎已经修改规则
				if(err && err.Msg === 'chooseAddress:fail auth deny' && err.Msg === 'chooseAddress:fail authorize no response'){
					this.reAuth()
				}
			},
			async reAuth(){
				const [err2,confirmResult] =  await uni.showModal({
					content:'检测到您没打开地址权限，是否去后台打开？',
					confirmText:'确认',
					cancelText:'取消'
				})
				if(confirmResult.cancel) return uni.$showMsg('您取消了地址授权')
				if(confirmResult.confirm) return uni.openSetting({
					success:(settingResult) => {
						if(!settingResult.authSetting['scope.address']) return uni.$showMsg('您取消了地址授权')
						if(settingResult.authSetting['scope.address']) return uni.$showMsg('授权成功')
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.address-border {
		display: block;
		width: 100%;
	}

	.address-choose-box {
		height: 90px;
		display: flex;
		justify-content: center;
		align-items: center;

		.btnChooseAddress {}
	}

	.address-info-box {
		// width: 100vw;
		font-size: 12px;
		height: 90px;
		display: flex;
		flex-direction: column;
		justify-content: center;
		padding: 0 5px;

		.row1 {
			display: flex;
			justify-content: space-between;

			.row1-left {
				.username {}
			}

			.row1-right {
				display: flex;
			}
		}

		.row2 {
			display: flex;
			// justify-content: space-between;
			align-items: center;
			margin-top: 10px;

			.row2-left {
				white-space: nowrap;
			}

			.row2-right {
				padding-left: 5px;
			}
		}
	}
</style>
