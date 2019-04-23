<template>
  <div class="service">
    <div>
 <!-- banner -->
    <div class="banner">
      <img src alt>
    </div>
    <div class="company">
      <div class="company_l">
        <div class="logo">
          <img :src="serverLogo" alt>
        </div>
      </div>
      <div class="company_r">
        <div class="t">
          <h4>{{serviceData.name}}</h4>
        </div>
        <div class="b">
          <span>联系方式：{{serviceData.phone}}</span>
          <a href="javascript:;" @click="getPhone">
            <img :src="phone" alt>点击拨打
          </a>
        </div>
      </div>
    </div>
    <div class="company_info">
      <ul class="tab">
        <li>
          <a href :class="[status == 1? 'active':'']" @click="toggleJs(1)">介绍</a>
        </li>
        <li>
          <a href :class="[status == 2? 'active':'']" @click="toggleLx(2)">联系</a>
        </li>
      </ul>
      <div class="content">
        <div class="cont" :hidden="recommend">
          <p>{{serviceData.introduce}} </p>
        </div>
        <div class="list" :hidden="contact">
          <li class="item">
            <div class="l">电话</div>
            <div class="r">{{serviceData.serviceTel}}</div>
          </li>
          <li class="item">
            <div class="l">地址</div>
            <div class="r">{{serviceData.address}}</div>
          </li>
          <li class="item">
            <div class="l">邮箱</div>
            <div class="r">
              <scroll-view :scroll-x="true">{{serviceData.email}}</scroll-view>
            </div>
          </li>
        </div>
      </div>
    </div>
    </div>
  </div>
</template>

<script>
import { md5, key, get, host, img } from "../../utils/api.js";
import { AMapWX } from "../../utils/amap-wx.js";
export default {
  data() {
    return {
      serverLogo: "../../../static/images/serverlogo.png",
      phone: "../../../static/images/phone.png",
      clientId: "",
      status: 1,
      recommend: false,
      contact: true,
      phoneNumber: "",
      serviceData:{}
    };
  },
  onLoad() {
    // this.getServices();
  },
  onShow() {
    this.getServices();
  },
  created() {},
  methods: {
    toggleJs(e) {
      if (e == 1) {
        this.status = 1;
        this.recommend = false;
        this.contact = true;
      }
    },
    toggleLx(e) {
      if (e == 2) {
        this.status = 2;
        this.contact = false;
        this.recommend = true;
      }
    },
    getPhone() {
      wx.makePhoneCall({
        // phoneNumber: this.data.serviceTel,
        phoneNumber: this.phoneNumber, //后台传来的电话
        success: function(res) {
          console.log("拨号成功！");
        },
        fail: function(res) {
          console.log("拨号失败！");
        }
      });
    },
    async getServices() {
      this.clientId = wx.getStorageSync("clientId");
      let sign = md5("clientId=" + this.clientId + "&key=" + key);
      const serviceData = await get("/api/company", {
        clientId: this.clientId,
        sign
      });
      if (serviceData.result.code === "200") {
        this.serviceData = serviceData.data;
        this.phoneNumber = serviceData.data.phone;
        console.log('serviceData',serviceData.data);
      } else {
        console.log("获取服务商失败");
      }
    }
  }
};
</script>

<style scoped lang="scss">
.service {
  height: 100%;
  background: #edf1f4;
  .banner {
    height: 398rpx;
    overflow: hidden;
    border: 1px solid #f00;
    img {
      width: 100%;
    }
  }
  .company {
    width: 100%;
    height: 150rpx;
    background: #fff;
    overflow: hidden;
    display: flex; /*设置为flex布局*/
    justify-content: space-around;
    margin-bottom: 20rpx;
    .company > div {
      flex: none;
    }
    .company_l {
      width: 228rpx;
      height: 150rpx;
      display: flex; /*设置为flex布局*/
      justify-content: center; /*水平居中*/
      align-items: center; /*垂直居中*/
      .logo {
        img {
          width: 168rpx;
          height: 60rpx;
        }
      }
    }
    .company_r {
      width: 522rpx;
      height: 150rpx;
      margin-top: 20rpx;
      .t {
        h4 {
          font-size: 32rpx;
          line-height: 54rpx;
          color: #333;
        }
      }
      .b {
        display: flex; /*设置为flex布局*/
        align-items: center; /*垂直居中*/
        span {
          float: left;
          font-size: 26rpx;
          color: #666;
        }
        a {
          width: 148rpx;
          height: 42rpx;
          margin-left: 10rpx;
          border: 1px solid #238f6b;
          border-radius: 3px;
          background: #fff;
          display: inline-block;
          font-size: 24rpx;
          line-height: 42rpx;
          color: #238f6b;
          float: left;
          display: flex; /*设置为flex布局*/
          justify-content: center; /*水平居中*/
          align-items: center; /*垂直居中*/
          img {
            width: 28rpx;
            height: 28rpx;
            margin-right: 6rpx;
          }
        }
      }
    }
  }
  .company_info {
    background: #fff;
    overflow: hidden;
    .tab {
      width: 100%;
      height: 80rpx;
      border-bottom: 1px solid #ddd;
      overflow: hidden;
      li {
        width: 50%;
        line-height: 80rpx;
        text-align: center;
        color: #aaaaaa;
        float: left;
        text-align: center;
        a {
          width: 120rpx;
          font-size: 36rpx;
          line-height: 77rpx;
          display: inline-block;
        }
        a.active {
          color: #238f6b;
          border-bottom: 3px solid #238f6b;
        }
        a:link {
          text-decoration: none;
          background: none;
        }
        a:visited {
          text-decoration: none;
          background: none;
        }
        a:hover {
          text-decoration: none;
          background: none;
        }
        a:active {
          text-decoration: none;
          background: none;
        }
      }
    }
    .content {
      .cont {
        padding: 40rpx 60rpx;
        font-size: 28rpx;
        color: #666;
        p {
          text-indent: 56rpx;
          line-height: 40rpx;
        }
        img {
          width: 100%;
          height: 314rpx;
          margin-top: 20rpx;
        }
      }
      .list {
        padding: 30rpx 33rpx;
        .item {
          padding: 40rpx 23rpx 40rpx 70rpx;
          border-bottom: 1px solid #ddd;
          text-align: left;
          overflow: hidden;
          .l {
            float: left;
            width: 30%;
            font-size: 28rpx;
            color: #666;
          }
          .r {
            float: left;
            width: 70%;
            font-size: 28rpx;
            color: #333;
          }
        }
      }
    }
  }
}
</style>