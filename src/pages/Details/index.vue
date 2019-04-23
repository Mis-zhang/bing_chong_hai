<template>
  <div class="details">
    <div class="coverPhoto" @click.stop="previewImage">
      <img :src="img + images" alt="">
    </div>
    <div class="wrapper">
      <div class="introduction" v-for="(item, index) in list" :key="index">
        <block v-if="item.tag === 'title'">
          <div class="title-title" :data-tag="item.tag">{{item.content}}</div>
        </block>
        <block v-if="item.tag === 'summary'">
          <div class="crops" :data-tag="item.tag">发病作物：{{item.content}}</div>
        </block>
        <block v-if="item.tag === 'level-2'">
          <div class="title" :data-tag="item.tag">
            <div class="title_txt">{{item.content}}</div>
          </div>
        </block>
        <block v-if="item.tag === 'para'">
          <div class="content" :class="introductionActive" :data-tag="item.tag">{{item.content}}</div>
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
    host,
    img
  } from '../../utils/api.js'
  export default {
    data() {
      return {
        cropId: '',
        plantId: '',
        clientId: '',
        list: [],
        images: '',
        img: ''
      }
    },
    mounted() {
      this.img = img
      this.clientId = wx.getStorageSync('clientId');
      this.cropId = this.$root.$mp.query.cropId
      console.log('this.cropId: ', this.cropId);
      this.plantId = this.$root.$mp.query.plantId
      console.log(' this.plantId : ',  this.plantId );
      this.getDetailsList()
    },
    onShareAppMessage: function() {

    },
    methods: {
      async getDetailsList() {
        wx.showLoading({
          title: '加载中'
        })
        let sign = md5('clientId=' + this.clientId + '&cropId=' + this.cropId + '&plantId=' + this.plantId + '&key=' + key)
        const DetailsList = await get('/api/plantinfo', {
          clientId: this.clientId,
          cropId: this.cropId,
          plantId: this.plantId,
          sign
        })
        if (DetailsList.result.code === "200") {
          wx.hideLoading();
          this.list = DetailsList.data
          for (var i = 0; i < this.list.length; i++) {
            if (this.list[i].tag === 'img') {
              this.images = this.list[i].content
            }
            if (this.list[i].tag === 'title') {
              wx.setNavigationBarTitle({
                title: this.list[i].content
              })
            }
          }
        }
      },
      previewImage() {
        wx.previewImage({
          current: this.img + this.images,
          urls: [this.img + this.images]
        })
      },
    }
  }
</script>

<style scoped lang="scss">
  .details {
    width: 100%;
    display: flex;
    flex-direction: column;
    .coverPhoto {
      width: 100%;
      height: 450rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      background: #f2f1f1;
      img {
        width: 100%;
        height: 100%;
      }
    }
    .wrapper {
      padding: 0rpx 50rpx;
      display: flex;
      flex-direction: column;
      .introduction {
        width: 100%;
        .title-title {
          padding: 30rpx 0rpx 30rpx 0rpx;
          color: #529000;
          font-weight: 700;
          font-size: 40rpx;
        }
        .title-title:last-child {
          border-bottom: 1rpx solid #ececec;
        }
        .title_copy {
          padding: 10rpx 0rpx 30rpx 0rpx;
          font-size: 30rpx;
          color: #898989;
        }
        .crops {
          padding: 30rpx 0rpx;
          color: #898989;
          font-size: 28rpx;
        }
        .crops:last-child {}
        .title {
          display: flex;
          flex-direction: row;
          justify-content: space-between;
          align-items: center;
          padding: 25rpx 0rpx;
          border-top: 1rpx solid #ededed;
          .title_txt {
            font-size: 35rpx;
            color: #000;
            font-weight: 700;
          }
          .title_img {
            font-size: 28rpx;
            color: #949494;
          }
        }
        .content {
          text-indent: 2em;
          font-size: 30rpx;
          color: #303030;
          line-height: 45rpx;
          display: block;
          text-overflow: clip;
          overflow: visible;
          height: 100%;
          padding-bottom: 20rpx;
          line-height: 50rpx;
        }
        .content:last-child {}
      }
    }
  }
</style>
