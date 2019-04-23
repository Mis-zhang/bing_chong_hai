<template>
  <div>
    <div class="ShareResults">
      <div class="cover" @click="previewImage">
        <img :src="cover" alt="">
      </div>
      <div class="loading" v-if="success">
        <div class="fail">
          <div class="fail_title">抱歉，没认出来~</div>
          <div class="fail_tips">再拍一次试试，请保证照片清晰，主体完整。</div>
        </div>
        <div class="again_photo" @click="Photo">再拍一张</div>
      </div>
      <div class="banner" v-else>
        <div class="results">
          <swiper class="swiper-block" @change="swiperChange" :previous-margin="'130rpx'" :next-margin="'130rpx'" :current="current">
            <block v-for="(item, index) in listShare" :key="index">
              <swiper-item class="swiper-item" data-id="1">
                <div class="theTitle">{{item.plantName}}<span v-if="item.severity === 1">严重</span><span v-else-if="item.severity === 0">一般</span></div>
                <div class="belong">{{item.cropName}} <span> (相似度：{{labelNnumber[index]}})</span></div>
                <image mode="aspectFill" :src="img + item.img" class="slide-image" :class="currentIndex == index ? 'active' : ''" :data-healthstatus="item.healthStatus" :data-plantid="item.plantId" :data-cropid="item.cropId" @click="ShareResult" />
                <div class="btn" @click="ShareResult" :data-healthstatus="item.healthStatus" :data-plantid="item.plantId" :data-cropid="item.cropId">查看结果</div>
              </swiper-item>
            </block>
          </swiper>
        </div>
      </div>
    </div>
    <canvas style="width: 300px; height: 200px;" canvas-id="myCanvas"></canvas>
  </div>
</template>

<script>
  import {
    md5,
    get,
    key,
    img,
    FillesPath
  } from '../../utils/api.js'
  export default {
    data() {
      return {
        indicatorDots: true,
        autoplay: false,
        interval: 3000,
        duration: 300,
        circular: true,
        currentIndex: 0,
        current: 0,
        success: false,
        listShare: [],
        img: '',
        clientId: Number,
        labelIds: '',
        cropId: Number,
        plantId: Number,
        cover: '',
        probability: '',
        predictData: [],
        predictId: [],
        labelIds: '',
        labelNnumber: []
      }
    },
    mounted() {
      this.clientId = wx.getStorageSync('clientId')
      this.labelNnumber = wx.getStorageSync('labelNnumber')
      this.labelIds = this.$root.$mp.query.labelIds
      this.cover = this.$root.$mp.query.tempFiles
      this.probability = this.$root.$mp.query.probability
      this.img = img
      if (this.probability >= 0 && this.probability < 0.5) {
        this.success = true
      } else if (this.probability >= 0.5 && this.probability <= 1) {
        this.success = false
        this.getShare()
      }
    },
    methods: {
      returnFloat(value) {
        var value = Math.round(parseFloat(value) * 100) / 100;
        var xsd = value.toString().split(".");
        if (xsd.length == 1) {
          value = value.toString() + ".00";
          return value;
        }
        if (xsd.length > 1) {
          if (xsd[1].length < 2) {
            value = value.toString() + "0";
          }
          return value;
        }
      },
      async getShare() {
        wx.showLoading({
          title: '加载中'
        })
        let sign = md5('labelIds=' + this.labelIds + '&key=' + key)
        const ShareData = await get('/api/detection', {
          labelIds: this.labelIds,
          sign
        })
        if (ShareData.result.code === "200") {
          wx.hideLoading(ShareData,'ShareData');
          console.log();
          this.listShare = ShareData.data
        }
      },
      ShareResult(e) {
        this.cropId = e.mp.target.dataset.cropid
        this.plantId = e.mp.target.dataset.plantid
        if (e.mp.target.dataset.healthstatus === 1) {
          wx.navigateTo({
            url: '/pages/Details/main?cropId=' + this.cropId + '&plantId=' + this.plantId
          })
        } else if (e.mp.target.dataset.healthstatus === 0) {
          wx.showModal({
            title: '提示',
            content: '此作物很健康',
            showCancel: false,
            confirmText: '知道啦',
            success(res) {
              if (res.confirm) {} else if (res.cancel) {}
            }
          })
        }
      },
      swiperChange(e) {
        this.currentIndex = e.mp.detail.current
      },
      animationfinish(e) {},
      Photo() {
        this.labelNnumber = []
        const ctx = wx.createCanvasContext('myCanvas')
        var that = this
        wx.chooseImage({
          count: 1,
          sizeType: ['original', 'compressed'],
          sourceType: ['album', 'camera'],
          success(res) {
            that.cover = res.tempFilePaths[0]
            const firstTempFiles = res.tempFilePaths[0]
            wx.getImageInfo({
              src: firstTempFiles,
              success(res) {
                var canvasWidth = res.width
                var canvasHeight = res.height;
                var tWidth = 800;
                var tHeight = 600;
                if (canvasWidth > tWidth || canvasHeight > tHeight) {
                  if (canvasWidth > canvasHeight * 1.8) {
                    tHeight = Math.floor(parseFloat(canvasHeight) * (parseFloat(tWidth) / parseFloat(canvasWidth)));
                  } else {
                    tWidth = Math.floor(parseFloat(canvasWidth) * (parseFloat(tHeight) / parseFloat(canvasHeight)));
                  }
                } else {
                  tWidth = canvasWidth;
                  tHeight = canvasHeight;
                }
                ctx.drawImage(firstTempFiles, 0, 0, tWidth, tHeight)
                ctx.draw()
                setTimeout(() => {
                  wx.canvasToTempFilePath({
                    fileType: 'jpg',
                    canvasId: 'myCanvas',
                    success(res) {
                      const tempFiles = res.tempFilePath
                      var uploadTask = wx.uploadFile({
                        url: FillesPath,
                        filePath: tempFiles,
                        name: 'image',
                        success(res) {
                          if (res.statusCode === 200) {
                            wx.showLoading({
                              title: '已上传 ' + '0' + ' %'
                            })
                            that.predictData = JSON.parse(res.data)
                            var probability = that.predictData[0].probability

                            for (var i = 0; i < that.predictData.length; i++) {
                              that.predictId.push(that.predictData[i].label_number)
                            }
                            for (var s = 0; s < that.predictData.length; s++) {
                              that.labelNnumber.push(that.returnFloat(that.predictData[s].probability) * 100 + '%')
                            }
                            that.labelIds = that.predictId.join(',')
                            wx.hideLoading()
                            wx.showToast({
                              title: '上传成功',
                              icon: 'success',
                              duration: 3000,
                              success(res) {
                                if (probability >= 0 && probability < 0.5) {
                                  that.success = true
                                } else if (probability >= 0.5 && probability <= 1) {
                                  that.success = false
                                  that.getShare()
                                }
                              }
                            })
                          }
                        }
                      })
                      uploadTask.onProgressUpdate((res) => {
                        if (res.progress >= 0 && res.progress < 100) {
                          wx.showLoading({
                            title: '已上传 ' + res.progress + ' %'
                          })
                        }
                      })
                    }
                  })
                }, 100)
              }
            })
          }
        })
      },
      previewImage() {
        wx.previewImage({
          current: this.cover,
          urls: [this.cover]
        })
      }
    }
  }
</script>

<style scoped lang="scss">
  .ShareResults {
    display: flex;
    width: 100%;
    height: 100%;
    position: relative;
    flex-direction: column;
    align-items: center;
    position: fixed;
    left: 0rpx;
    top: 0rpx;
    z-index: 9999;
    .cover {
      width: 100%;
      height: 420rpx;
      position: relative;
      z-index: 9999;
      img {
        width: 100%;
        height: 420rpx;
      }
    }
    .loading {
      width: 100%;
      height: 100%;
      background: #777;
      display: flex;
      align-items: center;
      flex-direction: column;
      .fail {
        width: 700rpx;
        height: 250rpx;
        background: #fff;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        border-radius: 10rpx;
        box-shadow: 0px 0px 30rpx rgba(0, 0, 0, 1);
        .fail_title {
          width: 100%;
          display: flex;
          justify-content: center;
          align-items: center;
          font-size: 35rpx;
          color: #000;
          font-weight: 550;
          padding: 20rpx 0rpx;
        }
        .fail_tips {
          width: 100%;
          display: flex;
          justify-content: center;
          align-items: center;
          font-size: 26rpx;
          color: #aaaaaa;
          padding: 20rpx 0rpx;
        }
      }
      .again_photo {
        width: 300rpx;
        height: 100rpx;
        border-radius: 10rpx;
        display: flex;
        justify-content: center;
        align-items: center;
        color: #000;
        font-size: 26rpx;
        background: #fff;
        margin-top: 70rpx;
      }
    }
    .banner {
      width: 100%;
      height: 100%;
      position: fixed;
      bottom: 0;
      left: 0;
      z-index: 8888;
      background: rgba(255, 255, 255, .3);
      display: flex;
      flex-direction: column;
      align-items: center;
      .results {
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        margin-top: 450rpx;
        .swiper-block {
          height: 100%;
          width: 100%;
          .swiper-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            overflow: unset;
            .theTitle {
              width: 100%;
              display: flex;
              justify-content: center;
              align-items: center;
              color: #000;
              font-size: 40rpx;
              font-weight: 700;
              padding-bottom: 20rpx;
              span {
                margin-left: 10rpx;
                color: #a6a6a6;
                font-size: 25rpx;
              }
            }
            .belong {
              width: 100%;
              display: flex;
              justify-content: center;
              align-items: center;
              color: #666;
              font-size: 30rpx;
              span {
                margin-left: 10rpx;
                color: #a6a6a6;
                font-size: 25rpx;
              }
            }
            .slide-image {
              height: 350rpx;
              width: 350rpx;
              border-radius: 50%;
              border: 15rpx solid #fff;
              background: #fff;
              box-shadow: 0px 0px 30rpx rgba(204, 204, 204, 1);
              z-index: 1;
              margin-top: 50rpx;
            }
            .slide-image.active {
              transform: scale(1.14);
              transition: all .2s ease-in 0s;
              z-index: 20;
            }
            .btn {
              width: 230rpx;
              height: 80rpx;
              display: flex;
              justify-content: center;
              align-items: center;
              border-radius: 50rpx;
              background: #45a922;
              color: #fff;
              margin-top: 100rpx;
              font-size: 33rpx;
            }
          }
        }
      }
    }
  }

  canvas {
    position: relative;
    top: 0rpx;
    left: 0rpx;
    z-index: 222;
  }
</style>
