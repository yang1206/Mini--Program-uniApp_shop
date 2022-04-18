<template>
	<view class="my-search-container" @click="searchBoxHandler" :style="{'background-color' : bgcolor}">
		<view class="my-search-box" :style="{'border-radius':radius + 'px'}">
			<uni-icons type="search" size="17"></uni-icons>
			<text class="placeholder">搜索</text>
		</view>
	</view>
</template>

<script>
	export default {
		name: "my-search",
		data() {
			return {

			};
		},
		props:{
			bgcolor:{
				type:String,
				default:'#c00000'
			},
			radius:{
				type:Number,
				default:18
			}
		},
		methods:{
			searchBoxHandler(){
				this.$emit('click')
			}
		}
	}
</script>

<style lang="scss">
	.my-search-container {
		height: 50px;
		// background-color: #c00000;
		display: flex;
		align-items: center;
		padding: 0 10px;

		.my-search-box {
			display: flex;
			justify-content: center;
			align-items: center;
			height: 36px;
			background-color: #fff;
			// border-radius: 18px;
			width: 100%;
			.placeholder {
				font-size: 15px;
				margin-left: 5px;
			}
		}
	}
</style>
