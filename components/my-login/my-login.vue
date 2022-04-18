<template>
	<view class="login-container">
		<uni-icons type="contact-filled" size="100" color="#AFAFAF"></uni-icons>
		<button type="default" @click="getUserInfo" class="btn-login">一键登录</button>
		<text class="tips-text">登录后尽享更多权益</text>
	</view>
</template>

<script>
	import {
		mapMutations,
		mapState
	} from 'vuex'
	export default {
		name: "my-login",
		data() {
			return {

			};
		},
		computed:{
			...mapState('m_user',['redirectInfo'])
		},
		methods: {
			...mapMutations('m_user', ['updateUserInfo','updateToken','updateRedirectInfo']),
			getUserInfo(e) {
				uni.getUserProfile({
					desc: '你的授权信息',
					success: (res) => {
						// 将信息存到 vuex 中
						// console.log(res)
						this.updateUserInfo(res.userInfo)
						this.getToken(res)
					},
					fail: (res) => {
						return uni.$showMsg('您取消了登录授权')
					}

				})
			},
			async getToken(info) {
				//获取code
				const [err, res] = await uni.login().catch(err => err)
				if (err || res.errMsg !== 'login:ok') return uni.$showMsg('登陆失败')

				const query = {
					code: res.code,
					encryptedData: info.encryptedData,
					iv: info.iv,
					rawData: info.rawData,
					signature: info.signature
				}
				const {
					data: loginResult
				} = await uni.$http.post('/api/public/v1/users/wxlogin', query)
				console.log(loginResult)
				// if(loginResult.meta.status !== 200)  return uni.$showMsg('登陆失败')
				// uni.$showMsg('登陆成功')
				
				
				//由于没有权限，所以乱写一个token
				this.updateToken('adasdasdsahdkjahskjdhjksahdjk')
				this.navigateBack()
			},
			navigateBack(){
				if(this.redirectInfo && this.redirectInfo.openType === 'switchTab'){
					uni.switchTab({
						url:this.redirectInfo.from,
						complete:() => {
							this.updateRedirectInfo(null)
						}
					})
				}
			}
		},
	}
</script>

<style lang="scss">
	.login-container {
		height: 750rpx;
		background-color: #f8f8f8;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		position: relative;

		&::after {
			content: '';
			display: block;
			width: 100%;
			height: 40px;
			background-color: white;
			position: absolute;
			bottom: 0;
			left: 0;
			border-radius: 100%;
			transform: translateY(50%);
		}

		.btn-login {
			width: 90%;
			border-radius: 100px;
			margin: 15px 0;
			background-color: #c00000;
			color: #fff;
		}

		.tips-text {
			font-size: 12px;
			color: gray;
		}
	}
</style>
