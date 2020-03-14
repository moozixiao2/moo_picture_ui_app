<template>
  <view>
    <view class="cate_tab">
      <view class="cate_tab_title">
        <view class="title_inner">
          <uni-segmented-control
            :current="current"
            :values="items.map(v => v.title)"
            @clickItem="onClickItem"
            style-type="text"
            active-color="#d21974"
          ></uni-segmented-control>
        </view>
        <!-- <view class="iconfont iconsearch"></view> -->
      </view>
      <scroll-view enable-flex scroll-y @scrolltolower="handleScrollTolower" class="cate_tab_content">
        <view class="cate_item" v-for="item in vertical" :key="item.id">
            <image :src="item.thumb" mode="widthFix"></image>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>

import { uniSegmentedControl } from "@dcloudio/uni-ui";
export default {
  components: {
    uniSegmentedControl
  },
  data() {
    return {
      items: [
        {
          title: "最新",
          order: "new"
        },
        {
          title: "热门",
          order: "hot"
        },
      ],
      current: 0,
      id: -1,
      // 接口参数
      params: {
          limit: 24,
          skip: 0,
          order: 'new'
      },
      vertical: [],
      hasMore: true
    };
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }
      else {
          // 点击了相同的标题
          return;
      }
      this.params.order = this.items[e.currentIndex].order;
      // 拼接vertical数据时，点击分段器时，vertical存在上一分段数据
      this.params.skip = 0;
      this.vertical = [];
      this.getData();
    },

    // 获取数据
    async getData () {
        // this.currentIndex === 0 ? this.params.order = 'new' : this.params.order = 'hot';
        const result = await this.request({
            url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
            data: this.params
        });
        // console.log(result);
        if(result.res.vertical.length === 0) {
            uni.showToast({
                title: '没有下一页了',
                duration: 2000
            });
            this.hasMore = false;
            return;
        }
        this.vertical = [...this.vertical, ...result.res.vertical];
    },

    // 滚动
    handleScrollTolower() {
        if(this.hasMore) {
            this.params.skip += this.params.limit;
            this.getData();
        }else {
            uni.showToast({
                title: '没有下一页了',
                duration: 2000
            });
        }
    }
  },
  onLoad (options) {
      this.id = options.id;
      this.getData();
  }
};
</script>

<style lang="scss" scoped>
.cate_tab{
  .cate_tab_title{
    position: relative;
    .title_inner{
      width: 60%;
      margin: 0 auto;
    }
    .iconsearch {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 5%;
    }
  }
}

.cate_tab_content {
    display: flex;
    flex-wrap: wrap;
    height: calc(100vh - 36px);
    .cate_item {
        width: 33.33%;
        border: 5rpx solid #fff; 
        image {

        }
    }
}
</style>