<template>
  <div class="package">
    <div class="list">
      <div class="l">
        <img src="../../../../static/images/historylistpic.png" alt>
      </div>
      <div class="r">
        <div class="t">
          <h2>苹果灰斑病</h2>
          <p>番t茄（98%）</p>
        </div>
        <div class="b">
          <p>上传时间</p>
          <p>2019年6月1日 18:12</p>
        </div>
      </div>
    </div>
     <div class="list">
      <div class="l">
        <img src="../../../../static/images/historylistpic.png" alt>
      </div>
      <div class="r">
        <div class="t">
          <h2>苹果灰斑病</h2>
          <p>番t茄（98%）</p>
        </div>
        <div class="b">
          <p>上传时间</p>
          <p>2019年6月1日 18:12</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  },
  methods: {}
};
</script>

<style scoped lang="scss">
.package {
  height: 100%;
  background: #edf1f4;
  .list {
    display: flex;
    padding: 24rpx 20rpx;
    margin-bottom: 20rpx;
    justify-content: space-around;
    background: #fff;
    .l {
      width: 241rpx;
      img {
        width: 241rpx;
        height: 181rpx;
        border-radius: 4px;
      }
    }
    .r {
      flex: 1;
      padding-left:23rpx; 
      display: flex;
      flex-direction: column;
      .t {
        display: flex;
        margin-top: 10rpx;
        h2 {
          font-size: 28rpx;
          line-height: 60rpx;
          color: #333333;
        }
        p {
          font-size: 24rpx;
           line-height: 60rpx;
           margin-left: 10rpx;
          color: #aaaaaa;
        }
      }
      .b {
        display: flex;
        p {
          font-size: 24rpx;
          line-height: 38rpx;
          color: #aaaaaa;
          &:last-child{
            margin-left: 10rpx;
          }
        }
        
      }
    }
  }
}
</style>