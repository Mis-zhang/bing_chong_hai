<template>
  <div class="package">
    <block v-if="FollowList.length != 0">
    <div class="list" v-for="(item, index) in FollowList" :key="index">
      <div class="l">
        <div class="user">
          <div class="title_img" :id="item.clientId" @click="getUserInfo(item.clientId)">
            <img
              :src="item.headPortrait"
              alt
            >
          </div>
          <div class="text">
            <div class="name">{{item.name}}</div>
            <div class="fans">粉丝:{{item.fansNum}}</div>
          </div>
        </div>
      </div>
      <div class="r">
        <block v-if="item.followStatus == 1">
            <div class="block" @click="getfollowStatus('add')">
            <img
              src="../../../../static/images/add.png"
              alt
            > &nbsp;关注
          </div>
        </block>
        <block v-else-if="item.followStatus == 2">
            <div class="block_ok" @click="getfollowStatus('ok')">
            <img
              src="../../../../static/images/ok.png"
              alt
            > &nbsp;已关注
          </div>
        </block>
        <block v-else-if="item.followStatus == 3">
            <div class="block_huxiang" @click="getfollowStatus('mutual')">
              互相关注
          </div>
        </block>
      </div>
    </div>
    </block>
    <block v-else>
    <div class="nothing">
      <div class="nothing_img">
        <img src="../../../../static/images/none.png" alt="">
      </div>
      <div class="nothing_text">暂无内容</div>
    </div>
    </block>
  </div>
</template>

<script>
import { md5, key, get, host, img } from "../../../utils/api.js";
const Base64 = require('js-base64').Base64;
export default {
  data() {
    return {
      clientId: Number,
      type: "",
      FollowList: [],
      a: false
    };
  },
  onLoad() {

  },
  onShow() {
     this.type = this.$root.$mp.query.type;
     this.clientId = wx.getStorageSync('clientId');
    var type = parseInt(this.type);
    console.log("this.type", this.type);
    switch (type) {
      case 1:
        wx.setNavigationBarTitle({
          title: "关注"
        });
        break;
      case 2:
        wx.setNavigationBarTitle({
          title: "粉丝"
        });
        break;
    }
    this.getFollowList()
  },
  methods: {
    async getFollowList() {
      let sign = md5("clientId=" + this.clientId + "&type=" + this.type + "&key=" + key);
      const FollowListData = await get("/api/client/follow/list", {
        clientId: this.clientId,
        type: this.type,
        sign
      });
      console.log('FollowListData', FollowListData);
      if (FollowListData.result.code === "200") {
        FollowListData.data.map((item) => {
          item.name = Base64.decode(item.name)
          return item
        })
        this.FollowList = FollowListData.data
        // this.nickName = Base64.decode(FollowListData.data.name)
      } else {
        console.log("获取服务商失败");
      }
    },
    getUserInfo(browseClientId) {
      wx.navigateTo({
        url: '../userInfo/main?browseClientId=' + browseClientId,
      });
    },
    getfollowStatus(flag) {
      if(flag == 'add') {
        wx.showToast({
          title: '关注成功',
          icon: 'success',
          duration: 1500,
          mask: true,
          success: (result)=>{
            this.getFollowList()
          }
        });
      }else if(flag == 'ok') {
        wx.showModal({
          title: '提示',
          content: '确定不再关注此人？',
          showCancel: true,
          cancelText: '取消',
          cancelColor: '#aaaaaa',
          confirmText: '确定',
          confirmColor: '#e67549',
          success: (result) => {
            if(result.confirm){
               this.getFollowList()
            }
          },
          fail: ()=>{},
          complete: ()=>{}
        });
      }else if(flag == 'mutual') {
        return
      }
    }
  }
};
</script>

<style scoped lang="scss">
.package {
  height: 100%;
  background: #edf1f4;
  .list {
    padding: 31rpx;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: #fff;
    border: 1px solid #eee;
    .l {
      display: flex;
      flex-direction: column;
      .user {
        display: flex;
        .title_img {
          width: 100rpx;
          height: 100rpx;
          border-radius: 50%;
          border: 1px solid red;
          img {
            width: 100rpx;
            height: 100rpx;
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
        width: 120rpx;
        height: 44rpx;
        border: solid 1px #238f6b;
        font-size: 24rpx;
        color: #238f6b;
        display: flex;
        justify-content: center;
        align-items: center;
        img {
          width: 16rpx;
          height: 16rpx;
        }
      }
      .block_ok {
        width: 120rpx;
        height: 44rpx;
        border: solid 1px #aaaaaa;
        font-size: 24rpx;
        color: #aaaaaa;
        display: flex;
        justify-content: center;
        align-items: center;
        img {
          width: 16rpx;
          height: 16rpx;
        }
      }
      .block_huxiang {
        width: 120rpx;
        height: 44rpx;
        border: solid 1px #aaaaaa;
        font-size: 24rpx;
        color: #aaaaaa;
        display: flex;
        justify-content: center;
        align-items: center;
      }
    }
  }
  .nothing {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    .nothing_img {
      width: 430rpx;
      height: 400rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      img {
        width: 100%;
        height: 100%;
      }
    }
    .nothing_text {
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 32rpx;
	font-weight: normal;
	font-stretch: normal;
	color: #aaaaaa;
    }
  }
}
</style>
