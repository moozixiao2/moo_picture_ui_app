<template>
  <scroll-view @scrolltolower="handleTolower" scroll-y class="album_view">
    <!-- 轮播图 开始 -->
    <!-- 
      自动轮播 autoplay
      指示器 indicator-dots
      衔接轮播 circular
     -->
    <view class="album_swiper">
      <swiper class="swiper" 
        indicator-dots
        autoplay
        circular
      >
        <swiper-item v-for="item in banners" :key="item.id">
          <image :src="item.thumb"></image>
        </swiper-item>
          </swiper>
    </view>
    <!-- 轮播图 结束 -->

    <!-- 列表 开始 -->
    <view class="album_list">
      <navigator class="album_item" v-for="item in albums" :key="item.id" :url="`/pages/album/index?id=${item.id}`">
        <view class="album_img">
          <image mode="aspectFill" :src="item.cover"></image>
        </view>
        <view class="album_info">
          <view class="album_name">{{item.name}}</view>
          <view class="album_desc">{{item.desc}}</view>
          <view class="album_btn">
            <view class="album_attention"> + 关注</view>
          </view>
        </view>
      </navigator>
    </view>
    <!-- 列表 结束 -->
  </scroll-view>
</template>

<script>
export default {
  data () {
    return {
      // 接口参数
      params: {
        limit: 8,
        order: 'new',
        skip: 0
      },
      // 轮播图
      banners: [],
      // 专辑
      albums: [],
      // 是否还有下一页
      hasMore: true
    }
  },
  methods: {
    // 接口数据
    getData () {
      this.request({
        url: 'http://157.122.54.189:9088/image/v1/wallpaper/album',
        data: this.params
      })
      .then(result => {
        // console.log(result)
        // 是否还有下一页
        if(result.res.album.length === 0) {
          uni.showToast({
            title: '没有下一页了',
            duration: 2000
          });
          this.hasMore = false;
          return;
        }

        if(this.banners.length === 0) {
          // 第一次请求
          this.banners = result.res.banner;
        }
        this.albums = [...this.albums, ...result.res.album];
      })
    },
    // 滚动触底事件
    handleTolower () {
      /* 
        skip += limit
        重新请求数据
        数据叠加
      */
     if(this.hasMore) {
       this.params.skip += this.params.limit;
       this.getData();
     } else {
       uni.showToast({
         title: '没有下一页了',
         duration: 2000
       });
     }
    }
  },
  mounted () {
    uni.setNavigationBarTitle({
      title: '专辑'
    });
    this.getData();
  }
}
</script>

<style lang='scss' scoped>
.album_view {
  height: calc(100vh - 36px);
}
.album_swiper {
  .swiper {
    // 2.3 原图的宽高比例
    height: calc(750rpx / 2.3);
    image {
      height: 100%;
    }
  }
}

.album_list {
  padding: 10rpx;
  .album_item {
    padding: 10rpx 0;
    display: flex;
    justify-content: space-between;
    border-bottom: solid 1rpx #ddd;
    .album_img {
      flex: 1;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      padding: 0 10rpx;
      overflow: hidden;
      .album_name {
        font-size: 30rpx;
        color: #000;
        padding: 10rpx 0;
      }

      .album_desc {
        font-size: 24rpx;
        padding: 10rpx 0;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
      }

      .album_btn {
        padding: 10rpx;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          color: $color;
          border: 1rpx solid $color;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>