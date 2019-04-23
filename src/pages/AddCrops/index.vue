<template>
  <div class="addCrops">
    <div class="title">请选择（最多{{qty}}种）您种植的作物</div>
    <div class="crops">
      <ul class="list">
        <li class="item" v-for="(item, index) in newListCrops" :key="index">
          <div class="item_img" :class="{'done':item.done}" @click="toggle(index)">
            <img :src="img + item.logo" alt="">
          </div>
          <div class="item_title">{{ item.nameZh }}</div>
        </li>
      </ul>
    </div>
    <div class="sumbit">
      <h2 @click="sumbit">保存</h2>
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
        listCrops: [],
        arr: [],
        addId: [],
        newArr: [],
        clientId: Number,
        img: '',
        newListCrops: [],
        cropIds: [],
        qty: Number
      };
    },
    onShow() {
      this.img = img
      this.clientId = wx.getStorageSync('clientId');
      this.getCrops()
    },
    onShareAppMessage: function() {},
    methods: {
      async getCrops() {
        wx.showLoading({
          title: '加载中'
        })
        this.newListCrops = []
        let sign = md5('clientId=' + this.clientId + '&key=' + key)
        const data = await get('/api/crops', {
          clientId: this.clientId,
          sign
        })
        if (data.result.code == '200') {
          wx.hideLoading();
          this.qty = data.data.qty
          this.listCrops = data.data.list
          for (var i = 0; i < this.listCrops.length; i++) {
            for (var s = 0; s < this.listCrops[i].crops.length; s++) {
              this.newListCrops.push({
                crops: this.listCrops[i].crops[s].crops,
                id: this.listCrops[i].crops[s].id,
                logo: this.listCrops[i].crops[s].logo,
                nameZh: this.listCrops[i].crops[s].nameZh,
                nameEn: this.listCrops[i].crops[s].nameEn,
                parentId: this.listCrops[i].crops[s].parentId,
                status: this.listCrops[i].crops[s].status,
                done: false
              })
            }
          }
        }
      },
      unique(arr) {
        this.newArr = [arr[0]];
        for (var i = 1; i < arr.length; i++) {
          if (this.newArr.indexOf(arr[i]) == -1) {
            this.newArr.push(arr[i]);
          }
        }
        return this.newArr;
      },
      toggle(i) {
        this.newListCrops[i].done = !this.newListCrops[i].done;
      },
      async postId(id) {
        let sign = md5('clientId=' + this.clientId + '&cropIds=' + id + '&key=' + key)
        const data = await get('/api/checkcrop', {
          clientId: this.clientId,
          cropIds: id,
          sign
        })
        if (data.result.code === '200') {
          wx.switchTab({
            url: '/pages/Home/main'
          })
        }
      },
      sumbit() {
        this.cropIds = []
        this.arr = this.newListCrops.filter(v => v.done);
        for (var i = 0; i < this.arr.length; i++) {
          this.cropIds.push(this.arr[i].id)
        }
        var ids = this.cropIds.join(',')
        if (this.arr.length === 0) {
          wx.switchTab({
            url: '/pages/Home/main'
          })
        } else if (this.arr.length > this.qty) {
          wx.showModal({
            title: "提示",
            content: "请选择（最多" + this.qty + "种）您种植的作物",
            showCancel: false,
            confirmText: '我知道了',
          });
        } else {
          this.postId(ids)
        }
      }
    }
  };
</script>

<style scoped lang="scss">
  .addCrops {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    position: relative;
    background: #fcfcfc;
    .title {
      width: 100%;
      font-size: 30rpx;
      color: #548b09;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 40rpx 0rpx;
      box-shadow: 0 15rpx 15rpx #ececec;
    }
    .crops {
      height: 100%;
      display: flex;
      padding-bottom: 150rpx;
      .list {
        display: flex;
        flex-wrap: wrap;
        height: 100%;
        padding: 0rpx 100rpx;
        overflow: hidden;
        overflow: scroll;
        .item {
          width: 150rpx;
          padding-top: 50rpx;
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: center;
          padding: 40rpx 17.999rpx;
          .item_img {
            width: 150rpx;
            height: 150rpx;
            display: flex;
            justify-content: center;
            align-items: center;
            border-radius: 50%;
            background: #ffffff;
            img {
              width: 100rpx;
              height: 100rpx;
              display: block;
            }
          }
          .item_img.done {
            border: 4rpx solid #F60;
            box-sizing: border-box;
          }
          .item_title {
            padding-top: 20rpx;
            font-size: 30rpx;
            color: #000;
          }
        }
      }
    }
    .sumbit {
      width: 100%;
      height: 130rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      box-shadow: 0 -5rpx 5rpx #ececec;
      position: fixed;
      bottom: 0;
      left: 0;
      h2 {
        font-size: 38rpx;
        font-weight: 700;
        letter-spacing: 5rpx;
        color: #548b09;
      }
    }
  }
</style>
