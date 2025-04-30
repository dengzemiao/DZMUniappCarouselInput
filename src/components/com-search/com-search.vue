<template>
  <view
    class="com-search"
    :style="bodyStyleComputed"
  >
    <!-- 搜索容器 -->
    <view class="com-search-container">
      <!-- 搜索框图标 -->
      <view
        v-if="usePrefixIcon"
        class="com-search-input-icon"
        :style="prefixIconStyleComputed"
      >
        搜索图标
      </view>
      <!-- 搜索框容器 -->
      <view
        class="com-search-input-container"
        :style="inputContainerStyleComputed"
      >
        <!-- 输入框 -->
        <easy-input
          v-if="useInput"
          v-model="inputValue"
          class="com-search-input"
          :confirm-type="confirmType"
          :maxlength="maxlength"
          :focus="focused"
          :placeholder="placeholder"
          :inputStyle="inputStyleComputed"
          :placeholderStyle="placeholderStyleComputed"
          @input="onChange"
          @focus="onFocus"
          @blur="onBlur"
          @confirm="onConfirm"
        />
        <!-- 滚动内容 -->
        <view
          v-else
          class="com-search-carousel"
          :style="carouselStyleComputed"
          @click="touchCarousel"
        >
          <view
            class="com-search-carousel-item"
            v-for="(item, index) in carousel"
            :key="index"
            :style="carouselItemStyleComputed"
          >
            {{ item }}
          </view>
        </view>
      </view>
      <!-- 搜索框清除 -->
      <view
        v-if="useClearIcon && useInput && !!inputValue && inputValue.length > 0"
        class="com-search-input-clear"
        :style="clearIconStyleComputed"
        @click="touchClear"
      >
        清空
      </view>
    </view>
    <!-- 弹簧 -->
    <view class="com-search-spacer"></view>
    <!-- 搜索按钮 -->
    <view
      v-if="useSearchButton"
      class="com-search-button"
      :style="searchButtonStyleComputed"
      @click="touchSearch"
    >
      {{ searchButtonText }}
    </view>
  </view>
</template>

<script>
// EasyInput 是基于 uni-easyinput 的组件调整的：https://uniapp.dcloud.net.cn/component/uniui/uni-easyinput.html
import EasyInput from './easyinput.vue'
export default {
  components: {
    EasyInput,
  },
  props: {
    // 这里使用 px 单位，是因为滚动内容使用 translateY 进行移动，如果使用 rpx 单位，会导致移动的距离不准确，数值越大，误差越大
    // 主容器默认高度（单位：px），优先使用 bodyStyle 的 height，没有则使用 bodyHeight
    bodyHeight: {
      type: Number | String,
      default: 40,
    },
    // 主容器样式
    bodyStyle: {
      type: Object,
      default: () => ({}),
    },
    // 启用左边前缀图标
    usePrefixIcon: {
      type: Boolean,
      default: true,
    },
    // 左边前缀图标样式
    prefixIconStyle: {
      type: Object,
      default: () => ({}),
    },
    // 启用右边搜索清除图标
    useClearIcon: {
      type: Boolean,
      default: true,
    },
    // 启用右边搜索清除图标聚焦
    useClearIconFocus: {
      type: Boolean,
      default: true,
    },
    // 右边搜索清除图标样式
    clearIconStyle: {
      type: Object,
      default: () => ({}),
    },
    // 启用右边搜索按钮
    useSearchButton: {
      type: Boolean,
      default: true,
    },
    // 右边搜索按钮文本
    searchButtonText: {
      type: String,
      default: '搜索',
    },
    // 右边搜索按钮样式
    searchButtonStyle: {
      type: Object,
      default: () => ({}),
    },
    // 中间搜索框容器样式
    inputContainerStyle: {
      type: Object,
      default: () => ({}),
    },
    // 中间使用输入框，true 使用输入框，false 使用轮播
    useInput: {
      type: Boolean,
      default: true,
    },
    // 输入框值
    value: {
      type: String | undefined,
      default: undefined,
    },
    // 中间输入框最大长度，设置为 -1 的时候不限制最大长度
    maxlength: {
      type: Number,
      default: -1,
    },
    // 中间输入框确认类型
    confirmType: {
      type: String,
      default: 'done',
    },
    // 中间输入框是否聚焦
    focus: {
      type: Boolean,
      default: false,
    },
    // 中间输入框样式
    inputStyle: {
      type: Object,
      default: () => ({}),
    },
    // 中间输入框提示
    placeholder: {
      type: String,
      default: '请输入内容',
    },
    // 中间输入框提示样式
    placeholderStyle: {
      type: Object,
      default: () => ({}),
    },
    // 中间滚动内容
    carousel: {
      type: Array,
      default: () => ([]),
    },
    // 中间滚动内容间隔(单位：ms)
    carouselInterval: {
      type: Number,
      default: 3000,
    },
    // 中间滚动内容动画时间(单位：ms)
    carouselAnimationTime: {
      type: Number,
      default: 300,
    },
    // 中间滚动内容样式
    carouselStyle: {
      type: Object,
      default: () => ({}),
    },
    // 中间滚动内容项样式
    carouselItemStyle: {
      type: Object,
      default: () => ({}),
    },
  },
  watch: {
    // 输入框值
    value: {
      immediate: true,
      handler(newVal, oldVal) {
        this.inputValue = newVal || ''
      }
    },
    // 滚动内容
    carousel: {
      immediate: true,
      handler(newVal, oldVal) {
        this.initCarousel()
      }
    },
    // 输入框是否聚焦
    focus: {
      immediate: true,
      handler(newVal, oldVal) {
        this.focused = newVal
      }
    }
  },
  computed: {
    bodyStyleComputed () {
      return this.obj2strStyle({
        ...this.bodyStyle,
        'height': this.bodyStyle['height'] || (this.bodyHeight + 'px'),
        'width': this.bodyStyle['width'] || '100%',
        'background-color': this.bodyStyle['background-color'] || '#FFFFFF',
        'border-radius': this.bodyStyle['border-radius'] || '10rpx',
        'border': this.bodyStyle['border'] || '1rpx solid #D8D8D8',
      })
    },
    prefixIconStyleComputed () {
      return this.obj2strStyle({
        ...this.prefixIconStyle,
        'height': this.prefixIconStyle['height'] || '100%',
        'background-color': this.prefixIconStyle['background-color'] || '#FC8333',
        'padding': this.prefixIconStyle['padding'] || '0 10rpx',
      })
    },
    inputContainerStyleComputed () {
      return this.obj2strStyle({
        ...this.inputContainerStyle,
        'padding': this.inputContainerStyle['padding'] || '0 10rpx',
      })
    },
    inputStyleComputed () {
      return this.obj2strStyle({
        ...this.inputStyle,
        'height': this.inputStyle['height'] || '100%',
        'font-size': this.inputStyle['font-size'] || '28rpx',
      })
    },
    placeholderStyleComputed () {
      return this.obj2strStyle({
        ...this.placeholderStyle,
        'font-size': this.placeholderStyle['font-size'] || '28rpx',
      })
    },
    carouselStyleComputed () {
      return this.obj2strStyle({
        ...this.carouselStyle,
        'height': this.carouselStyle['height'] || (this.bodyHeight + 'px'),
        'width': this.carouselStyle['width'] || '100%',
      })
    },
    carouselItemStyleComputed () {
      return this.obj2strStyle({
        ...this.carouselItemStyle,
        'width': this.carouselItemStyle['width'] || '100%',
        'height': this.carouselItemStyle['height'] || '100%',
        'color': this.carouselItemStyle['color'] || '#999999',
        'font-size': this.carouselItemStyle['font-size'] || '28rpx',
        'transform': 'translateY(-' + this.carouselIndex * this.bodyHeight + 'px)',
        'transition': this.carouselIndex === 0 ? 'none' : 'all ' + this.carouselAnimationTime + 'ms',
      })
    },
    clearIconStyleComputed () {
      return this.obj2strStyle({
        ...this.clearIconStyle,
        'height': this.clearIconStyle['height'] || '100%',
        'background-color': this.clearIconStyle['background-color'] || '#FC8333',
        'padding': this.clearIconStyle['padding'] || '0 10rpx',
      })
    },
    searchButtonStyleComputed () {
      return this.obj2strStyle({
        ...this.searchButtonStyle,
        'width': this.searchButtonStyle['width'] || '100rpx',
        'height': this.searchButtonStyle['height'] || '100%',
        'background-color': this.searchButtonStyle['background-color'] || '#00FF00',
      })
    },
  },
  data() {
    return {
      // 输入框是否聚焦
      focused: false,
      // 中间滚动内容索引
      carouselIndex: 0,
      // 中间滚动内容定时器
      carouselTimer: null,
      // 输入框值
      inputValue: '',
    }
  },
  beforeDestroy() {
    // 如果滚动内容定时器存在，则清除
    this.removeCarouselTimer()
  },
  methods: {
    // 对象转字符串 style
    obj2strStyle(obj) {
      let style = '';
      for (let key in obj) {
        const val = obj[key];
        style += `${key}:${val};`;
      }
      return style;
    },
    // 初始化中间滚动内容
    initCarousel() {
      // 如果禁用输入框，则开启滚动
      if (!this.useInput && this.carousel.length > 1) {
        if (!this.carouselTimer) {
          // 初始化中间滚动内容索引
          this.carouselIndex = 0
          // 设置中间滚动内容定时器
          this.carouselTimer = setInterval(() => {
            // 下标
            const nextIndex = this.carouselIndex + 1
            // 如果下标大于数组长度，可能动态调整滚动数据了，则重置为0
            if (nextIndex > this.carousel.length - 1) {
              this.carouselIndex = 0
            } else {
              this.carouselIndex = nextIndex % this.carousel.length
            }
          }, this.carouselInterval)
        }
      } else {
        // 如果滚动内容定时器存在，则清除
        this.removeCarouselTimer()
      }
    },
    // 移除中间滚动内容定时器
    removeCarouselTimer() {
      if (this.carouselTimer) {
        clearInterval(this.carouselTimer)
        this.carouselTimer = null
      }
    },
    // 点击滚动内容
    touchCarousel() {
      this.$emit('onCarousel', this.carousel[this.carouselIndex] || '')
    },
    // 点击清除
    touchClear() {
      if (this.useClearIconFocus) {
        this.$nextTick(() => {
          this.focused = true
        })
      }
      this.inputValue = ''
      this.$emit('onClear')
      this.onChange('')
      // this.onSearch()
    },
    // 点击搜索
    touchSearch() {
      if (this.useInput) {
        this.$emit('onSearch', this.inputValue)
      } else {
        this.$emit('onSearch', this.carousel[this.carouselIndex] || '')
      }
    },
    // 输入框值改变
    onChange(e) {
      this.$emit('onChange', e)
    },
    // 输入框聚焦
    onFocus(e) {
      this.$nextTick(() => {
        this.focused = true
      })
      this.$emit('onFocus', e.target.value, this.focused)
    },
    // 输入框失焦
    onBlur(e) {
      this.focused = false
      this.$emit('onBlur', e.target.value, this.focused)
    },
    // 输入框回车
    onConfirm(e) {
      this.$emit('onSearch', e)
    }
  }
}
</script>

<style scoped>
.com-search {
  display: flex;
  align-items: center;
  box-sizing: border-box;
}
.com-search-container {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
}
.com-search-input-container {
  flex: 1;
  height: 100%;
  display: flex;
}
.com-search-input {
  width: 100%;
  height: 100%;
  border: none;
  outline: none;
}
.com-search-input .is-input-border {
  border: none;
}
.com-search-input-icon {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}
.com-search-input-clear {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}
.com-search-button {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}
.com-search-spacer {
  flex: 1;
}
.com-search-carousel {
  flex: 1;
  overflow: hidden;
}
.com-search-carousel-item {
  display: flex;
  align-items: center;
  flex-shrink: 0;
}
</style>