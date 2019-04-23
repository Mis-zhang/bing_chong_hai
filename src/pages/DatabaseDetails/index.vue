<template>
  <div class="wrapper">
    <div class="wrapper_title">所有病虫害</div>

    <div class="cetory">
      <scroll-view class="content" enable-back-to-top :scroll-into-view="toView" :scroll-y="scrollY" :scroll-with-animation="scrollWithAnimation" @scroll="onPageScroll">
        <div v-for="(group, groupIndex) in listMain" :key="group.id" :id="'inToView'+group.id" :data-id='group.id'>
          <div class="address_top">{{group.region}}</div>
          <div v-for="(item, itemIndex) in group.brands" :key="item.id" class="list">
            <div class="address_bottom" :data-id="item.id" @click="toDatails(item.cropId, item.id)">
              <div class="item_img">
                <img :src="img + item.img" alt="" mode="aspectFit">
              </div>
              <div class="item_wrapper">
                <div class="item_name">{{ item.diseasesName }}</div>
                <div class="item_title">{{ item.nameZh }}</div>
              </div>
            </div>
          </div>
        </div>
      </scroll-view>
      <!-- <div class="list-fixed" :class="fixedTitle=='' ? 'hide':''" :style="'transform:translate3d(0,'+fixedTop+'px,0);'">
            <div class="fixed-title">
              {{fixedTitle}}
            </div>
          </div> -->
      <div class="orientation_region">
        <block v-for="item in listMain" :key="item.id">
          <div class="orientation_city" :class="isActive==item.id ? 'active':''" @click="scrollToViewFn" :data-id="item.id">
            {{item.region}}
          </div>
        </block>
      </div>
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
        scrollY: true,
        scrollWithAnimation: true,
        isActive: null,
        listMain: [],
        listTitles: [],
        fixedTitle: null,
        toView: 'inToView0',
        oHeight: [],
        scroolHeight: 0,
        number: 0,
        log: '',
        img: '',
        cropId: Number,
      }
    },
    onLoad() {
      this.img = img
      this.cropId = this.$root.$mp.query.cropId
      this.getDatabase()
    },
    onShareAppMessage: function() {

    },
    methods: {
      async getDatabase() {
        wx.showLoading({
          title: '加载中'
        })
        let sign = md5('cropId=' + this.cropId + '&key=' + 　key)
        const data = await get('/api/databank', {
          cropId: this.cropId,
          sign
        })
        if (data.result.code == '200') {
          wx.hideLoading();
          var someTtitle = null
          var someArr = []
          for (var i = 0; i < data.data.length; i++) {
            var newBrands = data.data[i].list
            if (data.data[i].tag != someTtitle) {
              someTtitle = data.data[i].tag
              var newObj = {
                id: i,
                region: someTtitle,
                brands: []
              }
              someArr.push(newObj)
            }
            newObj.brands = newBrands
          }
          this.listMain = someArr
          this.isActive = this.listMain[0].id,
            this.fixedTitle = this.listMain[0].region
        }
      },
      scrollToViewFn: function(e) {
        var that = this;
        var _id = e.mp.target.dataset.id;
        for (var i = 0; i < that.listMain.length; ++i) {
          if (that.listMain[i].id === _id) {
            that.isActive = _id,
              that.toView = 'inToView' + _id
            break
          }
        }
      },
      toBottom: function(e) {},
      onPageScroll: function(e) {
        this.scroolHeight = e.mp.detail.scrollTop
        for (let i in this.oHeight) {
          if (e.mp.detail.scrollTop < this.oHeight[i].height) {
            this.isActive = this.oHeight[i].key,
              this.fixedTitle = this.oHeight[i].name
            return false;
          }
        }
      },
      toDatails(cropid, plantid) {
        wx.navigateTo({
          url: "../Details/main?cropId=" + cropid + '&plantId=' + plantid
        });
      }
    },
    mounted() {
      var that = this
      for (let i = 0; i < this.listMain.length; ++i) {
        wx.createSelectorQuery().select('#inToView' + that.listMain[i].id).boundingClientRect(function(rect) {
          that.number = rect.height + that.number;
          var newArry = [{
            'height': that.number,
            'key': rect.dataset.id,
            "name": that.listMain[i].region
          }]
          that.oHeight = that.oHeight.concat(newArry)
        }).exec();
      };
    }
  }
</script>

<style scoped lang="scss">
  .wrapper {
    width: 100%;
    height: 100%;
    position: fixed;
    .wrapper_title {
      color: #606060;
      font-size: 30rpx;
      font-weight: bold;
      padding: 20rpx 40rpx;
    }
  }

  .cetory {
    width: 100%;
    height: 100%;
    .content {
      padding-bottom: 100rpx;
      box-sizing: border-box;
      height: 100%;
      position: fixed;
      .address_top {
        height: 56rpx;
        line-height: 56rpx;
        background: #ebebeb;
        color: #999;
        font-size: 28rpx;
        padding: 0 20rpx;
      }
      .list {
        width: 100%;
        display: flex;
        flex-direction: column;
        .address_bottom {
          background: #fff;
          padding: 15rpx 40rpx;
          display: flex;
          flex-direction: row;
          .item_img {
            width: 150rpx;
            height: 150rpx;
            background: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            img {
              display: block;
              width: 100%;
              height: 100%;
              border-radius: 50%;
            }
          }
          .item_wrapper {
            flex: 1;
            margin-left: 30rpx;
            margin-right: 50rpx;
            display: flex;
            flex-direction: column;
            align-items: flex-start;
            justify-content: center;
            border-bottom: 2rpx #ebebeb solid;
            .item_name {
              width: 100%;
              color: #000;
              font-weight: bold;
              font-size: 35rpx;
              padding: 5rpx 0rpx;
            }
            .item_title {
              width: 100%;
              color: #a2a2a2;
              font-size: 30rpx;
              padding: 10rpx 0rpx;
            }
          }
        }
      }
      .list:last-child .address_bottom .item_wrapper {
        border: none;
      }
    }
    .list-fixed {
      position: fixed;
      width: 100%;
      z-index: 999;
      height: 56rpx;
      line-height: 56rpx;
      background: #ebebeb;
      color: #999;
      font-size: 28rpx;
      padding: 0 20rpx;
      z-index: 9999;
      .fixed-title {
        color: #67b801;
        font-weight: bold;
      }
    }
    .orientation_region {
      width: 35rpx;
      font-size: 22rpx;
      position: fixed;
      top: 50%;
      right: 20rpx;
      transform: translate(0, -50%);
      background: transparent;
      .orientation_city {
        width: 35rpx;
        height: 35rpx;
        margin: 5rpx 0rpx;
        display: flex;
        justify-content: center;
        align-items: center;
        color: #000;
        text-align: center;
      }
      .orientation_city.active {
        color: #fff;
        background: #67b801;
        border-radius: 50%;
      }
    }
  }
</style>
