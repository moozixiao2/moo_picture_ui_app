<template>
  <view>
      <!-- 用户信息 开始 -->
        <view class="user_info">
            <view class="user_icon">
                <image mode="widthFix" :src="imgDetail.user.avatar"></image>
            </view>
            <view class="user_desc">
                <view class="user_name">{{imgDetail.user.name}}</view>
                <view class="user_time">{{imgDetail.natime}}</view>
            </view>
        </view>
      <!-- 用户信息 结束 -->

      <!-- 高清大图 开始 -->
      <view class="high_img">
          <image mode="widthFix" :src="imgDetail.thumb"></image>
      </view>
      <!-- 高清大图 结束 -->

      <!-- 点赞 开始 -->
      <view class="user_rank">
          <view class="rank">
              <text class="iconfont icondianzan">{{imgDetail.rank}}</text>
          </view>
          <view class="user_collect">
              <text class="iconfont iconshoucang">收藏</text>
          </view>
      </view>
      <!-- 点赞 结束 -->

      <!-- 专辑 开始 -->
      <view class="album_warp" v-if="album.length">
          <view class="album_title">相关</view>
          <view class="album_list">
              <view class="album_item" v-for="item in album" :key="item.id">
                  <view class="album_cover">
                      <image mode="aspectFill" :src="item.cover"></image>
                  </view>
                  <view class="album_info">
                      <view class="album_info_text">专辑</view>
                    <view class="album_name">{{item.name}}</view>
                    <view class="iconfont iconiconfontjiantou4"></view>
                  </view>
              </view>
          </view>
      </view>
      <!-- 专辑 结束 -->
      <!-- 热门评论 开始 -->
      <view class="comment hot" v-if="hot.length">
          <view class="comment_title">
              <text class="iconfont iconhot1"></text>
              <text class="comment_text">热门评论</text>
          </view>
          <view class="comment_list">
              <view class="comment_item"
                v-for="item in hot"
                :key="item.id"
              >
                <!-- 用户信息 -->
                <view class="comment_user">
                    <!-- 用户头像 -->
                    <view class="user_icon">
                        <image mode="widthFix" :src="item.user.avatar"></image>
                    </view>
                    <!-- 用户名称 -->
                    <view class="user_name">
                        <view class="user_nickname">{{item.user.name}}</view>
                        <view class="user_time">{{item.cnTime}}</view>
                    </view>
                    <!-- 用户徽章 -->
                    <view class="user_badge">
                        <image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon"></image>
                    </view>
                </view>
                <!-- 评论数据 -->
                <view class="comment_desc">
                    <view class="comment_content">{{item.content}}</view>
                    <view class="comment_like">
                        <text class="iconfont icondianzan">{{item.size}}</text>
                    </view>
                </view>
              </view>
          </view>
      </view>
      <!-- 热门评论 结束 -->
      <!-- 最新评论 开始 -->
      <view class="comment new" v-if="comment.length">
          <view class="comment_title">
              <text class="iconfont iconpinglun"></text>
              <text class="comment_text">最新评论</text>
          </view>
          <view class="comment_list">
              <view class="comment_item"
                v-for="item in comment"
                :key="item.id"
              >
                <!-- 用户信息 -->
                <view class="comment_user">
                    <!-- 用户头像 -->
                    <view class="user_icon">
                        <image mode="widthFix" :src="item.user.avatar"></image>
                    </view>
                    <!-- 用户名称 -->
                    <view class="user_name">
                        <view class="user_nickname">{{item.user.name}}</view>
                        <view class="user_time">{{item.cnTime}}</view>
                    </view>
                    <!-- 用户徽章 -->
                    <view class="user_badge">
                        <image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon"></image>
                    </view>
                </view>
                <!-- 评论数据 -->
                <view class="comment_desc">
                    <view class="comment_content">{{item.content}}</view>
                    <view class="comment_like">
                        <text class="iconfont icondianzan">{{item.size}}</text>
                    </view>
                </view>
              </view>
          </view>
      </view>
      <!-- 最新评论 结束 -->
  </view>
</template>

<script>
import moment from 'moment';
// 设置语言为中文
moment.locale('zh-cn');
export default {
    data () {
        return {
            // 图片详情对象（有用户头像）
            imgDetail: {},
            // 专辑数据
            album: [],
            // 最新评论
            comment: [],
            // 热门评论
            hot: []
        }
    },
    onLoad() {
        // console.log(getApp().globalData);
        const {imgList, imgIndex} = getApp().globalData;
        this.imgDetail = imgList[imgIndex];
        this.imgDetail.newThumb = this.imgDetail.thumb + this.imgDetail.rule.replace('$<Height>', 360);
        // xx年前
        this.imgDetail.natime = moment(this.imgDetail.atime * 1000).fromNow();

        // 图片详情id, 调用接口
        this.getComments(this.imgDetail.id);
    },
    methods: {
        getComments(id) {
            this.request({
                url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
            })
            .then(result => {
                // console.log(result);
                this.album = result.res.album;
                // 数组中的对象添加一个时间属性 xx月前
                result.res.hot.forEach(v => v.cnTime = moment(v.atime * 1000).fromNow());
                result.res.comment.forEach(v => v.cnTime = moment(v.atime * 1000).fromNow());

                this.comment = result.res.comment;
                this.hot = result.res.hot;
            })
        }
    },
}
</script>

<style lang='scss' scoped>
.user_info {
    display: flex;
    padding: 20rpx;
  .user_icon {
      padding: 0 20rpx;
    image {
      width: 88rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    .user_name {
        color: #000;
        font-weight: 600;
    }

    .user_time {
        padding: 10rpx 0;
        color: #ddd;
        font-style: 24rpx;
    }
  }
}

.user_rank {
    display: flex;
    height: 80rpx;
    border: solid 5rpx #eee;
  .rank {
      flex: 1;
      display: flex;
      justify-content: center;
      align-items: center;
    text {
    }
  }

  .user_collect {
      flex: 1;
      display: flex;
      justify-content: center;
      align-items: center;
    text {

    }
  }
}

.high_img {
    image {
        border-bottom: 1rpx solid #eee;
    }
}

// 专辑
.album_warp {
    padding: 20rpx;
  .album_title {
      padding: 10rpx 0;
  }

  .album_list {
    .album_item {
        display: flex;
        padding: 10rpx 0;
        border-bottom: 10rpx solid #eee;
      .album_cover {
          flex: 1;
        image {
            width: 180rpx;
            height: 180rpx;
        }
      }

      .album_info {
          flex: 3;
          padding-left: 20rpx;
          position: relative;
        .album_info_text {
            width: 100rpx;
            height: 50rpx;
            background-color: $color;
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .album_name {
            padding: 10rpx 0;
            color: #888;
        }

        .iconfont {
            position: absolute;
            top: 50%;
            right: 10%;
            color: #000;
            transform: translateY(-50%);
            font-size: 40rpx;
        }
      }
    }
  }
}

// 热门评论
.comment {
  .comment_title {
    padding: 15rpx;
    .iconfont {
        color: red;
        font-size: 40rpx;
    }

    .comment_text {
        font-weight: 600;
        font-size: 36rpx;
        color: #666;
        margin-left: 10rpx;
    }
  }

  .comment_list {
    .comment_item {
        border-bottom: 15rpx solid #eee;
        // 用户信息
        .comment_user {
            display: flex;
            padding: 20rpx 0;
            .user_icon {
                width: 15%;
                display: flex;
                justify-content: center;
                align-items: flex-start;
                image {
                    width: 90%;
                }
            }

            .user_name {
                flex: 1;
                .user_nickname {
                    color: #777;
                }

                .user_time {
                    color: #ccc;
                    font-size: 24rpx;
                    padding: 5rpx;
                }
            }

            .user_badge {
                image {
                    width: 40rpx;
                    height: 40rpx;
                }
            }
        }
        // 评论
        .comment_desc {
            display: flex;
            padding: 30rpx 0;
            .comment_content {
                flex: 1;
                padding-left: 15%;
                color: #000;
            }

            .comment_like {
                text-align: right;
                .iconfont {

                }
            }
        }
    }
  }
}

/* 最新评论 */
.new{
    .iconpinglun{
        color: aqua !important;
    }
}
</style>