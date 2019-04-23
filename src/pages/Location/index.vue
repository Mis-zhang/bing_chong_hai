<template>
  <div class="package">
    <div class="btn_wrap">
      <picker class="btn_t" mode="region" :value="region" @change="bindRegionChange">
        <view class="picker">{{regionText}}</view>
      </picker>
      <div class="btn_b" @click="submitRegion">确定</div>
    </div>
  </div>
</template>

<script>
import { md5, key, get, host, img } from "../../utils/api.js";
export default {
  data() {
    return {
      multiIndex: [0, 0, 0],
      date: "2016-09-01",
      time: "12:01",
      regionText: "点击选择种植地区",
      region: "",
      clientId: "",
      isFrist: Number
    };
  },
  onLoad() {


  },
  onShow() {
    if (this.isFrist == 1) {
      console.log('this.isFrist : ', this.isFrist );
        wx.switchTab({
          url: "/pages/Location/main",
          success: result => {
          }
        });
        this.getLogin();
    } else {
       console.log('this.isFrist : ', this.isFrist );
      wx.switchTab({
          url: "/pages/Home/main",
          success: result => {

          }
        });
    }
  },
  mounted() {},
  onShareAppMessage: function() {},
  methods: {
    bindRegionChange(e) {
      this.regionText = e.mp.detail.value;
      this.region = e.mp.detail.code[2];
    },
    submitRegion() {
      if (this.regionText == "点击选择种植地区") {
        wx.showModal({
          title: "提示",
          content: "请选择种植地区",
          showCancel: true,
          cancelText: "取消",
          cancelColor: "#000000",
          confirmText: "确定",
          confirmColor: "#3CC51F",
          success: result => {
            if (result.confirm) {
              console.log("未选择点击确定！");
            }
          }
        });
        return;
      } else {
        wx.showModal({
          title: "提示",
          content:
            this.regionText +
            "将作为您的种植地区。种植地区选择后，将无法修改。",
          showCancel: true,
          cancelText: "取消",
          cancelColor: "#000000",
          confirmText: "确定",
          confirmColor: "#3CC51F",
          success: result => {
            if (result.confirm) {
              this.selectitem();
            }
          }
        });
      }
    },
    async selectitem() {
      let sign = md5(
        "clientId=" + this.clientId + "&region=" + this.region + "&key=" + key
      );
      const data = await get("/api/croplist", {
        clientId: this.clientId,
        region: this.region,
        sign
      });
      if (data.result.code == "200") {
        console.log("data", data);
        wx.switchTab({
          url: "/pages/Home/main",
          success: result => {
            console.log("成功跳转");
          }
        });
      }
    },
    async getLoginCode(code) {
      let sign = md5("code=" + code + "&key=" + key);
      const data = await get("/api/login", {
        code: code,
        sign: sign
      });
      if (data.result.code === "200") {
        console.log("登陆data: ", data);
        this.clientId = data.data.clientId;
        this.isFrist = data.data.isFrist;

        console.log("this.isFrist: ", this.isFrist);
        wx.setStorageSync("clientId", this.clientId);
      } else {
        console.log("登陆失败");
      }
    },
    getLogin() {
      var that = this;
      wx.login({
        success(res) {
          console.log("res: ", res);
          if (res.code) {
            that.code = res.code;
            that.getLoginCode(that.code);
          } else {
          }
        }
      });
    }
  }
};
</script>

<style scoped lang="scss">
.package {
  width: 100%;
  height: 100%;
  background: #edf1f4;
  .btn_wrap {
    height: 100%;
    display: flex;
    align-items: center;
    flex-direction: column;
    position: relative;
    .btn_t {
      display: flex;
      width: 670rpx;
      height: 88rpx;
      margin-top: 31rpx;
      text-align: center;
      align-items: center;
      justify-content: center;
      background-color: #ffffff;
      border-radius: 6px;
      font-size: 32rpx;
      color: #999999;
      .picker {
        width: 650rpx;
        margin: 0 10rpx;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        align-items: center;
      }
    }
    .btn_b {
      display: flex;
      width: 670rpx;
      height: 88rpx;
      position: absolute;
      bottom: 31rpx;
      align-content: flex-end;
      align-items: center;
      justify-content: center;
      background-color: #238f6b;
      border-radius: 6px;
      font-size: 32rpx;
      color: #ffffff;
    }
  }
}
</style>
