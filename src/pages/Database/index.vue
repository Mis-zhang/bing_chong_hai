<template>
  <div class="category">
    <div class="content">
      <scroll-view class="left" scroll-y="true">
        <div class="iconText" @click="selectitem(item.id,index)" v-for="(item, index) in listData" :class="[index==nowIndex?'active':'']" :key="index">
          {{item.nameZh}}
        </div>
      </scroll-view>
      <scroll-view class="right" scroll-y="true">
        <div class="bottom">
          <div @click="categoryList(item.id)" v-for="(item,index) in detailData" :key="index" class="item">
            <img :src="img + item.logo" alt="" mode="aspectFit">
            <span>{{item.nameZh}}</span>
          </div>
        </div>
      </scroll-view>
    </div>
  </div>
</template>

<script>
  import {
    md5,
    key,
    get,
    img
  } from "../../utils/api.js"
  export default {
    data() {
      return {
        parentId: "",
        nowIndex: 0,
        listData: [],
        detailData: [],
        clientId: Number,
        img: ''
      };
    },
    created() {
      this.clientId = wx.getStorageSync('clientId')
      this.img = img
      this.getListData();
    },
    mounted() {
      this.selectitem(this.parentId, this.nowIndex);
    },
    onShareAppMessage: function() {

    },
    methods: {
      async selectitem(id, index) {
        wx.showLoading({
          title: '加载中',
        });
        this.nowIndex = index;
        this.parentId = id
        let sign = md5('parentId=' + this.parentId + '&key=' + key)
        const data = await get("/api/croplist", {
          parentId: this.parentId,
          sign
        });
        if (data.result.code == '200') {
          wx.hideLoading();
          this.detailData = data.data
        }
      },
      async getListData() {
        let sign = md5('clientId=' + this.clientId + '&key=' + key)
        const data = await get("/api/crops", {
          clientId: this.clientId,
          sign
        });
        this.listData = data.data.list;
        this.parentId = data.data.list[0].id
      },
      categoryList(id) {
        wx.navigateTo({
          url: "../DatabaseDetails/main?cropId=" + id
        });
      }
    }
  };
</script>

<style scoped lang="scss">
  .database {
    width: 100%;
    height: 100%;
  }

  .category {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    .search {
      height: 88rpx;
      padding: 0 30rpx;
      background: #fff;
      display: flex;
      align-items: center;
      border-bottom: 1rpx solid #ededed;
      .ser {
        width: 690rpx;
        height: 56rpx;
        background: #ededed;
        border-radius: 8rpx;
        display: flex;
        align-items: center;
        justify-content: center;
        span {
          display: inline-block;
        }
        .icon {
          background: url('http://yanxuan.nosdn.127.net/hxm/yanxuan-wap/p/20161201/style/img/icon-normal/search2-2fb94833aa.png') center no-repeat;
          background-size: 100%;
          width: 28rpx;
          height: 28rpx;
          margin-right: 10rpx;
        }
      }
    }
    .content {
      flex: 1;
      background: #fff;
      display: flex;
      .left {
        width: 162rpx;
        height: 100%;
        text-align: center;
        background: #fcfcfc;
        .iconText {
          text-align: center;
          line-height: 90rpx;
          width: 162rpx;
          height: 90rpx;
          color: #333;
          font-size: 26rpx;
        }
        .active {
          color: #ab2b2b;
          font-size: 30rpx;
          color: #67b801;
          background: #fff;
        }
      }
      .right {
        flex: 1;
        height: 100%;
        padding: 0 30rpx 0 30rpx;
        background: #fff;
        .banner {
          width: 100%;
          height: 222rpx;
          margin-top: 20rpx;
          img {
            width: 100%;
            height: 100%;
          }
        }
        .bottom {
          display: flex;
          flex-wrap: wrap;
          padding-top: 20rpx;
          .item {
            width: 33.33%;
            text-align: center;
            margin-bottom: 20rpx;
            img {
              height: 144rpx;
              width: 144rpx;
              display: block;
              margin: 0 auto;
              border-radius: 5rpx;
            }
            span {
              font-size: 28rpx;
              color: #333;
            }
          }
        }
      }
    }
  }
</style>
