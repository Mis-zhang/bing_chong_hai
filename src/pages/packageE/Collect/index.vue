<template>
  <div class="package">
    <div class="post_wrap">
      <div class="posts" v-for="(item, index) in userListAll" :key="index">
        <div class="user_wrap">
          <div class="l">
            <div class="user_box">
              <div class="title_img">
                <img :src="item.clientHeadPortrait" alt>
              </div>
              <div class="text">
                <div class="name">{{item.clientName}}</div>
                <div class="fans">{{item.addTime}}</div>
              </div>
            </div>
          </div>
          <div class="r" @click="dotFn">
            <div class="block">
              <img :src="dot" alt>
            </div>
          </div>
        </div>
        <div class="text_wrap">
          <div>
            <span class="text">
              <span class="point">- 苹果灰斑病 -</span>害是什么原因引起的啊有没有人知道啊请问这个病虫害是什么原因引起的啊有没有人知道啊请问这个病虫害是什么原因引
              <span class="point">全文</span>
            </span>
          </div>
        </div>
        <div class="pic_wrap">
          <ul>
            <li class="item">
              <img src="../../../../static/images/tizipic.jpg" alt>
            </li>
            <li class="item">
              <img src="../../../../static/images/tizipic.jpg"  alt>
            </li>
            <li class="item">
              <img src="../../../../static/images/tizipic.jpg"  alt>
            </li>
          </ul>
        </div>
        <div class="review_wrap">
          <ul class="hudong">
            <li class="item">
              <img :src="shareIcon" alt>
              <span>转发</span>
            </li>
            <li class="item">
              <img :src="reviewIcon" alt>
              <span>201</span>
            </li>
            <li class="item">
              <img :src="praise" alt>
              <span>1245</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <div class="mask" catchtouchmove="catchTouch">
      <ul class="mask_list">
        <li>123</li>
        <li>456</li>
        <li>789</li>
      </ul>
    </div>
  </div>
</template>

<script>
import { md5, key, get, host, img } from "../../../utils/api.js";
import { AMapWX } from "../../../utils/amap-wx.js";
const Base64 =  require('js-base64').Base64;
// decode
export default {
  data() {
    return {
      userImg:'../../../../static/images/雨夹雪.png',
      dot:'../../../../static/images/dot.png',
      itemImg:'',
      shareIcon:'../../../../static/images/share.png',
      reviewIcon:'../../../../static/images/pl.png',
      praise:'../../../../static/images/dz.png',
      clientId: Number,
      type: Number,
      userListAll: [],
      messageList: [],
      keyword: '',
      page: 1,
      pagerow: 10
    };
  },
  onShow() {
    // console.log('5bCP5oKf8J+Is/CfkqQ=', Base);
    this.clientId = wx.getStorageSync("clientId");
    this.type = this.$root.$mp.query.type
    switch (this.$root.$mp.query.type) {
      case 5:
        wx.setNavigationBarTitle({
          title: "发布"
        });
        this.messageList= ['删除帖子', '取消']
        break;
      case 6:
        wx.setNavigationBarTitle({
          title: "收藏"
        });
        this.messageList= ['取消收藏', '取消']
        break;
    }
    this.getUserList(this.clientId, this.keyword, this.page, this.pagerow, this.type)
  },
  methods: {
    async getUserList(clientId, keyword, page, pagerow, type) {
      let sign = md5("clientId=" + clientId + "&keyword=" + keyword + "&page=" + page + "&pagerow=" + pagerow + "&type=" + type + "&key=" + key);
      const UserList = await get("/api/client/articles", {
        clientId,
        keyword,
        page,
        pagerow,
        type,
        sign
      });
      console.log('个人信息：--》',UserList);
      if (UserList.result.code === "200") {
        let userListAll = UserList.data
        userListAll.map((item) => {
          item.clientName = Base64.decode(item.clientName)
          item.addTime = '2019-12-01'
          return item
        })
        this.userListAll = userListAll
      } else {
        console.log("获取服务商失败");
      }
    },
    dotFn() {
      // wx.showModal({
      //   title: 'sda',
      //   content: '123211',
      //   showCancel: true,
      //   cancelText: '取消',
      //   cancelColor: '#000000',
      //   confirmText: '确定',
      //   confirmColor: '#3CC51F',
      //   success: (result) => {
      //     if(result.confirm){
      //     }
      //   },
      //   fail: ()=>{},
      //   complete: ()=>{}
      // });
    },
    catchTouch() {
      return;
    }
  }
};
</script>

<style scoped lang="scss">
.package {
  height: 100%;
  background: #edf1f4;
  position: relative;
  z-index: 100;
  /*帖子*/
  .post_wrap {
    background: #edf1f4;
    .title {
      width: 100%;
      height: 100rpx;
      background-color: #ffffff;
      border-bottom: 1px solid #edf1f4;
      .list {
        display: flex;
        justify-content: center;
        align-items: center;
        li {
          height: 100rpx;
          .biaoti {
            font-size: 28rpx;
            color: #333333;
            line-height: 98rpx;
            border-bottom: 1px solid #238f6b;
          }
        }
      }
    }
    .posts {
      margin-bottom: 20rpx;
      background: #fff;
      .user_wrap {
        padding: 20rpx 45rpx 0rpx 30rpx;
        display: flex;
        align-items: center;
        justify-content: space-between;
        background: #fff;
        // border: 1px solid #eee;
        .l {
          display: flex;
          flex-direction: column;
          .user_box {
            display: flex;
            .title_img {
              width: 100rpx;
              height: 100rpx;
              border-radius: 50%;
              border: 1px solid red;
              img {
                width: 100rpx;
                height: 100rpx;
                border-radius: 50%;
              }
            }
            .text {
              margin-left: 27rpx;
              padding-top: 15rpx;
              display: flex;
              flex-direction: column;
              .name {
                font-size: 28rpx;
                line-height: 36rpx;
                color: #333333;
              }
              .fans {
                font-size: 24rpx;
                line-height: 46rpx;
                color: #aaaaaa;
              }
            }
          }
        }
        .r {
          .block {
            height: 40rpx;
            display: flex;
            justify-content: center;
            align-items: center;
            img {
              width: 49rpx;
              height: 10rpx;
            }
          }
        }
      }
      .text_wrap {
        padding: 0rpx 45rpx 20rpx 30rpx;
        .text {
          font-size: 28rpx;
          color: #333333;
          .point {
            font-size: 28rpx;
            color: #e67549;
          }
        }
      }
      .pic_wrap {
        padding: 0rpx 45rpx 20rpx 30rpx;
        ul {
          display: flex;
          .item {
            flex: 1;
            text-align: center;
            img {
              width: 220rpx;
              height: 220rpx;
            }
          }
        }
      }
      .review_wrap {
        padding: 20rpx 45rpx 20rpx 30rpx;
        border-top: 1px solid #eee;
        .hudong {
          display: flex;
          .item {
             flex: 1;
            justify-content: center;
            font-size: 28rpx;
            text-align: center;
            color: #666666;
            align-items: center;
            display: flex;
            img {
              width: 28rpx;
              height: 28rpx;
              margin-right: 10rpx;
            }
          }
        }
      }
    }
  }
  /*遮罩*/
  .mask {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    z-index: 1000;
    // background: regba(0,0,0,.5);
    background: rgba(0, 0, 0, 0.5);
    .mask_list {
      width: 520rpx;
      height: 302rpx;
      background: #fff;
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
      border-top-left-radius:5rpx;
      border-top-right-radius:5rpx;
      li {
        width: 100%;
        height: 100rpx;
        display: flex;
        justify-content: center; //水平居中
        align-items: center; //垂直居中
        border-bottom: 1rpx solid #aaa;
        font-size: 28rpx;
        color: #333333;
        &:last-child {
          border-bottom: none;
        }
      }
    }
  }
}
</style>
