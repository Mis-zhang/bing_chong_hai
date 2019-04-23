<template>
    <div class="review_list">
        <div class="content">
            <ul class="list">
                <li class="item">
                    <div class="l">
                        <img src="../../../../static/images/阴天.png" alt>
                    </div>
                    <div class="r">
                        <div class="name">小悟空</div>
                        <div class="cont">啊实打实接口返回世纪东方很深刻的金凤凰是可敬的凤凰山科技的粉红色开奖号啥空间的好数据库</div>
                        <div class="time">
                            <div class="time_l">289129721</div>
                            <div class="time_r">
                                <img src="../../../../static/images/dz.png" alt>
                                <span>33</span>
                            </div>
                        </div>
                    </div>
                </li>
            </ul>
        </div>
        <div class="repay">
          <div class="repay_header">
            <div class="repay_title">18条回复</div>
          </div>
          <div class="repay_item">
                asd
          </div>
        </div>
    </div>
</template>

<script>
import { md5, get, host, key, img } from "../../../utils/api.js";
const Base64 = require('js-base64').Base64;
// decode
export default {
    data() {
        return {
            getClientId: Number,
            clientArticleId: Number,
            clientId: Number,
            evaluateId: Number,
            artId: Number,
            artInfoList: {},
            repayList: []
        }
    },
    onShow() {
        this.getClientId = wx.getStorageSync('clientId');
        this.clientArticleId = this.$root.$mp.query.clientArticleId
        this.clientId = this.$root.$mp.query.clientId
        this.evaluateId = this.$root.$mp.query.evaluateId
        this.artId = this.$root.$mp.query.artId
        // this.getArtInfo()
    },
    onShareAppMessage: function() {},
    methods: {
        // 回复列表
        async getArtInfo() {
            let _this = this
            let sign = md5("artId=" + this.artId + "&clientId=" + this.getClientId + "&key=" + key);
            const artInfoData = await get("/api/client/art/info", {
                artId: this.artId,
                clientId: this.clientId,
                sign
            });
            console.log('帖子详情页-->', artInfoData);
            if (artInfoData.result.code === "200") {
                this.artInfoList = artInfoData.data
                this.artInfoList.evaluateList.map((item) => {
                  if(item.id = _this.evaluateId) {
                    this.repayList = item.repayList
                  }
                  return item
                })
                console.log(this.repayList);
            } else {
                console.log("获取列表失败");
            }
        },
    }
}
</script>

<style scoped lang="scss">
.review_list {
    background: #edf1f4;
    width: 100%;
    min-height: 100%;
    position: relative;
    .content {
        background: #ffffff;
        .list {
            padding: 31rpx 45rpx 20rpx 28rpx;
            .item {
                display: flex;
                width: 100%;
                /*设置为flex布局*/
                // justify-content: space-around;
                .l {
                    width: 72rpx;
                    height: 72rpx;
                    border-radius: 50%;
                    img {
                        width: 72rpx;
                        height: 72rpx;
                        border-radius: 50%;
                    }
                }
                .r {
                    flex: 1;
                    margin-left: 16rpx;
                    border-bottom: 1px solid #eee;
                    .name {
                        font-size: 28rpx;
                        color: #666666;
                    }
                    .cont {
                        font-size: 28rpx;
                        color: #333333;
                        padding-bottom: 20rpx;
                    }
                    .time {
                        // height: 65rpx;
                        display: flex;
                        justify-content: space-around;
                        padding-bottom: 10rpx;
                        .time_l {
                            font-size: 24rpx;
                            color: #aaaaaa;
                            flex: 1;
                            text-align: center;
                            justify-content: flex-start;
                            color: #666666;
                            align-items: center;
                            display: flex;
                        }
                        .time_r {
                            flex: 1;
                            justify-content: flex-end;
                            font-size: 28rpx;
                            text-align: center;
                            color: #666666;
                            align-items: center;
                            display: flex;
                            img {
                                width: 28rpx;
                                height: 28rpx;
                                margin-right: 10rpx;
                            }
                        }
                    }
                }
                &:last-child .r {
                    border-bottom: none;
                }
            }
        }
    }
}
</style>
