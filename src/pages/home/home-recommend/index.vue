<template>
  <scroll-view class="recommend_view" @scrolltolower="handleTolower" scroll-y v-if="recommends.length > 0">
    <!-- 推荐 开始 -->
    <view class="recommend_wrap">
      <navigator class="recommend_item" v-for="item in recommends" :key="item.id" :url="`/pages/album/index?id=${item.target}`">
        <image mode="widthFix" :src="item.thumb"></image>
      </navigator>
    </view>
    <!-- 推荐 结束 -->

    <!-- 月份 开始 -->
    <view class="monthes_wrap">
      <view class="monenths_title">
        <view class="monthes_title_info">
          <view class="monthes_info">
            <text>{{monthes.DD}} / </text>
            {{monthes.MM}} 月
          </view>
          <view class="monthes_text">{{monthes.title}}</view>
        </view>
        <view class="monthes_title_more">更多 > </view>
      </view>
      <view class="monthes_content">
        <view class="monents_item" v-for="(item, index) in monthes.items" :key="item.id">
          <go-detail :list="monthes.items" :index="index">
            <image mode="aspectFill" :src="item.thumb + item.rule.replace('$<Height>', 360)"></image>
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 月份 结束 -->

    <!-- 热门 开始 -->
    <view class="hots_wrap">
      <view class="hots_title">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="hots_item" v-for="(item, index) in hots" :key="item.id">
          <go-detail :list="hots" :index="index">
            <image mode='widthFix' :src='item.thumb' ></image>
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 热门 结束 -->
  </scroll-view>
</template>

<script>
import moment from 'moment';
import goDetail from '../../../components/goDetail';
export default {
  components: {
    goDetail
  },
  data () {
    return {
      // 请求参数
      params: {
        // 要获取条数
        limit: 24,
        // 关键字
        order: "hot",
        // 要跳过几条
        skip: 0
      },
      // 推荐列表
      recommends: [],
      // 月份
      monthes: {},
      // 热门
      hots: [],
      // 是否有下一页
      hasMore: true
    }
  },
  mounted () {
    this.getData();
  },
  methods: {
    // 获取接口数据
    getData() {
      this.request({
        url: 'http://157.122.54.189:9088/image/v3/homepage/vertical',
        data: this.params,
      })
      .then(result => {
        // console.log(result)
        // 判断是否还有没下一页
        if(result.res.vertical.length === 0) {
          uni.showToast({
            title: '没有下一页了',
            duration: 2000
          });
          this.hasMore = false;
          return;
        }

        if(this.recommends.length === 0) {
          // 第一次发送请求
          this.recommends = result.res.homepage[1].items;
          this.monthes = result.res.homepage[2];
          // console.log(this.monthes.stime)
          // 将时间戳 改变
          this.monthes.MM = moment(this.monthes.stime).format("MM");
          this.monthes.DD = moment(this.monthes.stime).format("DD");
        }
        
        // 数组拼接
        this.hots = [...this.hots, ...result.res.vertical];
      })
    },
    // 滚动条触底事件
    handleTolower () {
      /* 
        skip += limit
        重新请求接口
        hots 数据叠加
      */
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
}
</script>

<style lang='scss' scoped>
.recommend_view {
  height: calc(100vh - 36px);
}
// 推荐
.recommend_wrap {
  display: flex;
  flex-wrap: wrap;
 .recommend_item {  
   width: 50%;
   border: 5rpx solid #fff;
 } 
}

// 月份
.monthes_wrap {
  .monenths_title {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20rpx;
    .monthes_title_info {
      color: $color;
      font-size: 30rpx;
      font-weight: 600;
      display: flex;
      .monthes_info {
        text {
          font-size: 36rpx;
        }
      }

      .monthes_text {
        font-size: 32rpx;
        color: #666;
        margin-left: 30rpx;
      }
    }

    .monthes_title_more {
      font-size: 24rpx;
      color: $color;
    }
  }

  .monthes_content {
    display: flex;
    flex-wrap: wrap;
    .monents_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}

// 热门
.hots_wrap {
  .hots_title {
    padding: 20rpx;
    text {  
      border-left: 12rpx solid $color;
      padding-left: 20rpx;
      font-size: 30rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hots_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      image {

      }
    }
  }
}
</style>