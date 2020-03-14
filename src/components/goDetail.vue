<template>
  <view @click="handleClick">
      <slot></slot>
  </view>
</template>

<script>
export default {
    props: {
        list: Array,
        index: Number
    },
    methods: {
        handleClick () {
          /* 
            将数据缓存下来
            点击跳转页面
           */  
           getApp().globalData.imgList = this.list;
           getApp().globalData.imgIndex = this.index;

           uni.navigateTo({
                url: '/pages/imgDetail/index'
           });
        }
    },
}
</script>

<style>

</style>