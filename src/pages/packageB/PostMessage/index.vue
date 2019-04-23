<template>
  <div class="package">
    <div class="text_wrap">
      <div class="textarea">
        <textarea
          :value="content"
          placeholder="想说点什么"
          @change="inputs"
        ></textarea>
        <p>{{prompt}}</p>
      </div>
      <div class="my_pic">
        <ul>
          <li
            v-for="(item, index) in upImg"
            :key="index"
            @longtap.stop="longPress"
          >
            <img
              :src="imgHttp + '/' + item"
              alt
            >
            <div
              class="close"
              v-if="isListImg"
              @click="removeImg"
            >
              <img
                :src="colse"
                alt
              >
            </div>
          </li>
          <li
            class="photograph"
            @click="chooseImage"
          >
            <div>
              <img
                src="../../../../static/images/photograph.png"
                alt
              >
            </div>
            <div>添加图片</div>
          </li>
        </ul>
      </div>
    </div>
    <div class="submit" @click="getSave">
      <div class="btn">发表</div>
    </div>
  </div>
</template>

<script>
import { md5, get, host, key, img } from "../../../utils/api.js";
export default {
  data() {
    return {
      clientId: Number,
      isListImg: false,
      listImg: "../../../../static/images/tizipic.jpg",
      colse: "../../../../static/images/close.png",
      content: "",
      pics: [],
      successImage: [],
      files: [],
      maxImg: 9,
      upImg: [],
      imgHttp: '',
      keyword: ''
    };
  },
  onShow() {
    this.clientId = wx.getStorageSync('clientId');
    this.imgHttp = img
  },
  onHide() { },
  onShareAppMessage: function () { },
  methods: {
    async getSave() {
      wx.showLoading({
        title: '帖子发表中...',
        mask: true,
      });
      let saveData = {
        clientId: this.clientId,
        content: this.content,
        imgs: this.upImg,
        keyword: this.keyword
      }
      let datas = JSON.stringify(saveData)
      let sign = md5("data=" + datas + "&key=" + key);
      const artSave = await get("/api/client/art/save", {
        data: datas,
        sign
      });
      console.log('上传状态：--》',artSave);
      if (artSave.result.code === "200") {
        wx.hideLoading();
        wx.showToast({
          title: '发表成功',
          icon: 'success',
          duration: 1500,
          mask: true,
          success: (result)=>{

          },
          fail: ()=>{},
          complete: ()=>{}
        });
        setTimeout(function() {
          wx.navigateBack({
            delta: 1
          });
    }, 1600);
      } else {
        console.log("获取服务商失败");
      }
    },
    chooseImage(e) {
      let i = 0;					// 多图上传时使用到的index
      let that = this;
      let max = that.maxImg;		//最大选择数
      let upLength;						//图片数组长度
      let imgFilePaths;				//图片的本地临时文件路径列表
      wx.chooseImage({
        count: max || 1,           //一次最多可以选择的图片张数
        sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
        sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
        success: function (res) {
          let len = that.files.concat(res.tempFilePaths);
          console.log(res);
          imgFilePaths = res.tempFilePaths;
          upLength = imgFilePaths.length;
          // if(len.length > max){
          //   // that.$mptoast('图片最多只能选择' + max);
          //   return false;
          // }
          /**
           * 上传完成后把文件上传到服务器
           */
          wx.showLoading({
            title: '上传中...',
          })
          that.upLoad(imgFilePaths, i, upLength);			//上传操作
        },
        fail: function () {
          console.log('fail');
        },
        complete: function () {
          console.log('commplete');
        }
      })
    },
    upLoad(imgPath, i, upLength) {
      console.log(this.clientId,'---->this.clientId');
      let that = this;
      //上传文件
      wx.uploadFile({
        url: host + '/api/img/up',
        filePath: imgPath[i],
        name: 'file',
        header: {
          "Content-Type": "multipart/form-data"
        },
        formData: {
          clientId: that.clientId
        },
        success: function (res) {
          console.log('上传成功' + i);
          // 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
          console.log(res,'res结果');
          let imgData = JSON.parse(res.data);
          console.log(imgData);
          that.upImg.push(imgData.data.url);
          console.log(that.upImg);
        },
        fail: function (res) {
          console.log(res);
          wx.hideLoading();
          wx.showModal({
            title: '错误提示',
            content: '上传图片失败',
            showCancel: false,
            success: function (res) { }
          })
        },
        complete: function () {
          i++;
          if (i == upLength) {
            wx.hideLoading();    //上传结束，隐藏loading
          } else {
            that.upLoad(imgPath, i, upLength);
          }
        }
      });
    },













    //字数限制
    inputs: function (e) {
      console.log(e);
      this.content = e.mp.detail.value
    },
    longPress() { //长按
      this.isListImg = true;
    },
    deleteImg(index) {
      let that = this
      wx.showModal({
        title: '提示',
        content: '确定删除这张照片吗？',
        confirmColor: '#c6ae6c',
        success(res) {
          if (res.confirm) {
            that.tempFilePathsArr.splice(index, 1)
          } else if (res.cancel) {
            console.log('用户点击取消')
          }
        }
      })
    },
  }
};
</script>

<style scoped lang="scss">
.package {
  height: 100%;
  background: #edf1f4;
  position: relative;
  z-index: 100;
  .text_wrap {
    background: #fff;
    padding: 34rpx 38rpx;
    overflow: hidden;
    .textarea {
      width: 100%;
      height: 400rpx;
      font-size: 28rpx;
      line-height: 42rpx;
      position: relative;
      color: #aaaaaa;
      textarea {
        width: 100%;
        height: 400rpx;
        color: #333333;
      }
      p {
        position: absolute;
        bottom: 0;
        left: 0;
        color: #e67549;
      }
    }
    .my_pic {
      ul {
        // display: flex;
        align-items: center;
        li {
          width: 180rpx;
          height: 180rpx;
          margin-right: 29rpx;
          margin-top: 29rpx;
          position: relative;
          float: left;
          img {
            width: 180rpx;
            height: 180rpx;
          }
          .close {
            img {
              width: 32rpx;
              height: 32rpx;
              position: absolute;
              top: 0;
              right: 0;
            }
          }
        }
        li.photograph {
          text-align: center;
          border: 1px dashed #ddd;
          align-content: center;
          justify-content: center;
          flex-direction: column;
          display: flex;
          font-size: 24rpx;
          line-height: 42rpx;
          color: #aaaaaa;
          img {
            width: 72rpx;
            height: 63rpx;
          }
        }
      }
    }
  }
  .submit {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 100rpx;
    background: #fff;
    display: flex; // justify-content: center;
    justify-content: flex-end;
    align-items: center;
    .btn {
      width: 190rpx;
      height: 66rpx;
      margin-right: 30rpx;
      display: flex;
      align-items: center;
      justify-content: center;
      background-color: #238f6b;
      border-radius: 4px;
      font-size: 28rpx;
      color: #ffffff;
    }
  }
}
</style>
