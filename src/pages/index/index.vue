<template>
	<view class="content">
		<com-search class="search-input" :value="value" focus></com-search>
		<com-search class="search-input" bodyHeight="100" @onChange="onChange" @onSearch="onSearch" @onCarousel="onCarousel" @onClear="onClear" @onFocus="onFocus" @onBlur="onBlur"></com-search>
		<com-search class="search-input" bodyHeight="100" :usePrefixIcon="false"></com-search>
		<com-search class="search-input" :useClearIcon="false"></com-search>
		<com-search class="search-input" :useSearchButton="false"></com-search>
		<com-search class="search-input" :useSearchButton="false" :usePrefixIcon="false" :useClearIcon="false"></com-search>
		<com-search class="search-input" :useInput="false" :carousel="carousel" @onCarousel="onCarousel"></com-search>
		<com-search class="search-input" :useSearchButton="false" :usePrefixIcon="false" :useClearIcon="false" :useInput="false" :carousel="carousel" @onCarousel="onCarousel"></com-search>
		<view class="header-view">
			<com-search
				:bodyStyle="{
					'background-color': '#1FB1CC',
				}"
				:placeholderStyle="{
					'color': '#FF0000',
				}"
				@onSearch="onSearch"
			></com-search>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			value: '热门搜索',
			carousel: []
		}
	},
	mounted() {
		setTimeout(() => {
			this.carousel = ['轮播1', '轮播2', '轮播3', '轮播4', '轮播5', '轮播6', '轮播7']
		}, 1000);
	},
	methods: {
		onChange(value) {
			console.log('输入框值改变', value);
		},
		onSearch(value) {
			console.log('点击搜索', value);
		},
		onCarousel(value) {
			console.log('点击轮播区域', value);
		},
		onClear() {
			console.log('点击清除');
		},
		onFocus(value) {
			console.log('输入框聚焦', value);
		},
		onBlur(value) {
			console.log('输入框失焦', value);
		}
	}
}
</script>

<style scoped>
.content {
  width: 100vw;
  height: 100vh;
	display: flex;
	flex-direction: column;
	align-items: center;
  background-color: #F0F0F0;
}
.search-input {
	/* 加 !important 是因为 com-search 组件内部有宽度样式，内部样式都是内联样式，会被覆盖 */
	width: 80% !important;
	margin-top: 20rpx;
}
.header-view {
	width: 100%;
	padding: 20rpx 40rpx 10rpx 40rpx;
	box-sizing: border-box;
}
</style>
