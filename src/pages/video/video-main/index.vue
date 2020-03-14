<template>
  <scroll-view class="video_main" scroll-y @scrollTolower="handleTolower" enable-flex>
      <view class="video_item"
        v-for="item in videowp"
        :key="item.id"
        @click="handleToVideo(item)"
      >
        <image mode="widthFix" :src="item.img"></image>
      </view>
  </scroll-view>
</template>

<script>
export default {
    props: {
        urlobj: Object
    },
    data () {
        return {
          videowp: [],
          hasMore: true  
        }
    },
    watch: {
        urlobj() {
            // 清空
            this.videowp = [];
            this.getData();
        }
    },
    mounted () {
        // console.log(this.urlobj);
        this.getData();
    },
    methods: {
        async getData() {
            const {url, params} = this.urlobj;
            const result = await this.request({
                url: url,
                data: params
            });

            if(result.res.videowp.length === 0) {
                this.hasMore = false;
                
                uni.showToast({
                    title: '没有下一页了',
                    duration: 2000,
                    icon: 'none'
                });

                return;
            }

            this.videowp = [...this.videowp, ...result.res.videowp];
            // console.log(result);
        },
        // 滚动
        handleTolower () {
            if(this.hasMore) {
                this.urlobj.params.skip += this.urlobj.params.limit;
                this.getData();
            }else {
                uni.showToast({
                    title: '没有下一页了',
                    duration: 2000,
                    icon: 'none'
                });
            }
        },
        // 图片点击事件
        handleToVideo(item) {
            // 数据全局共享中
            getApp().globalData.video = item;
            // 跳转
            uni.navigateTo({
                 url: '/pages/videoPlay/index'
            });
        }
    },
}
</script>

<style lang="scss" scoped>
.video_main {
    display: flex;
    flex-wrap: wrap;
    height: calc(100vh - 36px);
    .video_item {
        width: 33.33%;
        border: 5rpx solid #fff;
    }
}
</style>