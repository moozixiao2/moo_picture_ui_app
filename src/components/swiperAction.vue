/* 
    slot
    对外提供数据 滑动方向
 */
<template>
  <view @touchstart="handleTouchstart" @touchend="handleTouchend">
      <slot></slot>
  </view>
</template>

<script>
export default {
    data () {
        return {
            // 按下时间
            startTime: 0,
            // 按下坐标
            startX: 0,
            startY: 0
        }
    },
    methods: {
        // 按下屏幕
        handleTouchstart (event) {
            this.startTime = Date.now();
            this.startX = event.changedTouches[0].clientX;
            this.startY = event.changedTouches[0].clientY;
        },
        // 离开屏幕
        handleTouchend (event) {
            const endTime = Date.now();
            const endX = event.changedTouches[0].clientX;
            const endY = event.changedTouches[0].clientY;

            // 判断接下的时长
            if(endTime -this.startTime > 2000) {
                return;
            }

            // 滑动方向
            let direction = '';

            // 判断用户滑动距离 是否合法 合法：判断滑动方向
            if(Math.abs(endX - this.startX) > 10 && Math.abs(endY -  this.startY) < 10) {
                // 滑动方向
                direction = endX - this.startX > 0 ? 'right' : 'left';
            }else {
                return;
            }

            // 合法操作
            this.$emit("swiperAction", {direction});
        }
    }
}
</script>

<style>

</style>