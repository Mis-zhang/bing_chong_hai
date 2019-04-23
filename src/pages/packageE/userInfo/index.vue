<template>
    <div class="package">
        <div class="accredit_btn">

        </div>
        <div class="content">
            <div class="user_wrap">
                <div class="user">
                    <div class="l">
                        <div class="user_pic">
                            <img :src="UserInfoNumber.headPortrait" alt="">
                        </div>
                        <span class="name">
                  {{nickName}}
                </span>
                    </div>
                    <div class="r">
                        <block v-if="UserInfoNumber.followStatus == 1">
                            <div class="block">
                                <img src="../../../../static/images/add.png" alt> &nbsp;关注
                            </div>
                        </block>
                        <block v-else-if="UserInfoNumber.followStatus == 2">
                            <div class="block_ok">
                                <img src="../../../../static/images/ok.png" alt> &nbsp;已关注
                            </div>
                        </block>
                        <block v-else-if="UserInfoNumber.followStatus == 3">
                            <div class="block_huxiang">
                                互相关注
                            </div>
                        </block>
                        <div class="block active">
                            <img src="../../../../static/images/share_icon.png" alt> &nbsp;分享
                        </div>
                    </div>
                </div>
                <div class="list">
                    <ul>
                        <li @click="getIssue">
                            <a href>
                                <p>{{UserInfoNumber.articleNum}}</p>
                                <h4>发布</h4>
                            </a>
                        </li>
                        <li @click="getAttention">
                            <a href>
                                <p>{{UserInfoNumber.followNum}}</p>
                                <h4>关注</h4>
                            </a>
                        </li>
                        <li @click="getEnshrine">
                            <a href>
                                <p>{{UserInfoNumber.collectNum}}</p>
                                <h4>收藏</h4>
                            </a>
                        </li>
                        <li @click="getFans">
                            <a href>
                                <p>{{UserInfoNumber.fansNum}}</p>
                                <h4>粉丝</h4>
                            </a>
                        </li>

                    </ul>
                </div>
            </div>
            <div class="post_wrap">
                <div class="title">
                    <ul class="list">
                        <li>
                            <a class="biaoti">他的帖子</a>
                        </li>
                    </ul>
                </div>
                <div class="posts">
                    <div class="user_wrap">
                        <div class="l">
                            <div class="user_box">
                                <div class="title_img">
                                    <img src="../../../../static/images/雨夹雪.png" alt>
                                </div>
                                <div class="text">
                                    <div class="name">宫本武藏</div>
                                    <div class="fans">2019年3月11日 17:31</div>
                                </div>
                            </div>
                        </div>
                        <div class="r">
                            <div class="block">
                                <img src="../../../../static/images/dot.png" alt>
                            </div>
                        </div>
                    </div>
                    <div class="text_wrap">
                        <div>
                            <span class="text">
                    <span class="point">- 苹果灰斑病 -</span>害是什么原因引起的啊有没有人知道啊请问这个病虫害是什么原因引起的啊有没有人知道啊请问这个病虫害是什么原因引
                            <span class="point">全文</span>
                            </span>
                        </div>
                    </div>
                    <div class="pic_wrap">
                        <ul>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                        </ul>
                    </div>
                    <div class="review_wrap">
                        <ul class="hudong">
                            <li class="item">
                                <img src="../../../../static/images/share.png" alt>
                                <span>转发</span>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/pl.png" alt>
                                <span>201</span>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/dz.png" alt>
                                <span>1245</span>
                            </li>
                        </ul>
                    </div>
                </div>
                <div class="posts">
                    <div class="user_wrap">
                        <div class="l">
                            <div class="user_box">
                                <div class="title_img">
                                    <img src="../../../../static/images/雨夹雪.png" alt>
                                </div>
                                <div class="text">
                                    <div class="name">宫本武藏</div>
                                    <div class="fans">2019年3月11日 17:31</div>
                                </div>
                            </div>
                        </div>
                        <div class="r">
                            <div class="block">
                                <img src="../../../../static/images/dot.png" alt>
                            </div>
                        </div>
                    </div>
                    <div class="text_wrap">
                        <div>
                            <span class="text">
                    <span class="point">- 苹果灰斑病 -</span>害是什么原因引起的啊有没有人知道啊请问这个病虫害是什么原因引起的啊有没有人知道啊请问这个病虫害是什么原因引
                            <span class="point">全文</span>
                            </span>
                        </div>
                    </div>
                    <div class="pic_wrap">
                        <ul>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                        </ul>
                    </div>
                    <div class="review_wrap">
                        <ul class="hudong">
                            <li class="item">
                                <img src="../../../../static/images/share.png" alt>
                                <span>转发</span>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/pl.png" alt>
                                <span>201</span>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/dz.png" alt>
                                <span>1245</span>
                            </li>
                        </ul>
                    </div>
                </div>
                <div class="posts">
                    <div class="user_wrap">
                        <div class="l">
                            <div class="user_box">
                                <div class="title_img">
                                    <img src="../../../../static/images/雨夹雪.png" alt>
                                </div>
                                <div class="text">
                                    <div class="name">宫本武藏</div>
                                    <div class="fans">2019年3月11日 17:31</div>
                                </div>
                            </div>
                        </div>
                        <div class="r">
                            <div class="block">
                                <img src="../../../../static/images/dot.png" alt>
                            </div>
                        </div>
                    </div>
                    <div class="text_wrap">
                        <div>
                            <span class="text">
                    <span class="point">- 苹果灰斑病 -</span>害是什么原因引起的啊有没有人知道啊请问这个病虫害是什么原因引起的啊有没有人知道啊请问这个病虫害是什么原因引
                            <span class="point">全文</span>
                            </span>
                        </div>
                    </div>
                    <div class="pic_wrap">
                        <ul>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/tizipic.jpg" alt>
                            </li>
                        </ul>
                    </div>
                    <div class="review_wrap">
                        <ul class="hudong">
                            <li class="item">
                                <img src="../../../../static/images/share.png" alt>
                                <span>转发</span>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/pl.png" alt>
                                <span>201</span>
                            </li>
                            <li class="item">
                                <img src="../../../../static/images/dz.png" alt>
                                <span>1245</span>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { md5, key, get, host, img } from "../../../utils/api.js";
const Base64 = require('js-base64').Base64;
export default {
    data() {
        return {
            clientId: "",
            browseClientId: Number,
            UserInfoNumber: {},
            nickName: ''
        };
    },
    onLoad() {

    },
    onShow() {
        // console.log('解密',Base64.decode(Base64.encode('小悟🈳💤')));
        this.clientId = wx.getStorageSync("clientId");
        this.browseClientId = this.$root.$mp.query.browseClientId
        this.getUserInfo()
    },
    methods: {
        async getUserInfo() {
            let sign = md5("browseClientId=" + this.browseClientId + "&clientId=" + this.clientId + "&key=" + key);
            const UserInfo = await get("/api/client/center", {
                browseClientId: this.browseClientId,
                clientId: this.clientId,
                sign
            });
            console.log('用户信息：--》', UserInfo);
            if (UserInfo.result.code === "200") {
                this.UserInfoNumber = UserInfo.data
                this.nickName = Base64.decode(UserInfo.data.name)
            } else {
                console.log("获取服务商失败");
            }
        }
    }
};
</script>

<style scoped lang='scss'>
.package {
    height: 100%;
    background: #edf1f4; // .accredit_btn {
    //   height: 100%;
    //   display: flex;
    //   align-items: center;
    //   justify-content: center;
    //   button {
    //     width: 670rpx;
    //     height: 88rpx;
    //     background-color: #238f6b;
    //     border-radius: 12rpx;
    //     color:#fff;
    //   }
    // }
    .content {
        .user_wrap {
            background: #fff;
            margin-bottom: 20rpx;
            .user {
                height: 148rpx;
                border-bottom: 1px solid #eee;
                display: flex;
                align-items: center;
                justify-content: space-between;
                .l {
                    display: flex;
                    align-items: center;
                    justify-content: space-around;
                    .user_pic {
                        width: 100rpx;
                        height: 100rpx;
                        margin: 25rpx 30rpx;
                        border-radius: 50%;
                        border: 1px solid #eee;
                        .user-icon {
                            width: 100rpx;
                            height: 100rpx;
                            border-radius: 50%;
                            display: inline-block;
                            overflow: hidden;
                        }
                    }
                    .name {
                        font-size: 28rpx;
                        color: #333333;
                    }
                    .sq_btn {
                        width: 56rpx;
                        height: 42rpx;
                        background: #238f6b;
                    }
                }
                .r {
                    display: flex;
                    justify-content: space-around;
                    padding-right: 30rpx;
                    .block {
                        width: 120rpx;
                        height: 44rpx;
                        margin-left: 10rpx;
                        border: solid 1px #238f6b;
                        font-size: 24rpx;
                        color: #238f6b;
                        display: flex;
                        justify-content: center;
                        align-items: center;
                        img {
                            width: 20rpx;
                            height: 20rpx;
                        }
                    }
                    .block_ok {
                        width: 120rpx;
                        height: 44rpx;
                        margin-left: 10rpx;
                        border: solid 1px #aaaaaa;
                        font-size: 24rpx;
                        color: #aaaaaa;
                        display: flex;
                        justify-content: center;
                        align-items: center;
                        img {
                            width: 20rpx;
                            height: 20rpx;
                        }
                    }
                    .block_huxiang {
                        width: 120rpx;
                        height: 44rpx;
                        margin-left: 10rpx;
                        border: solid 1px #aaaaaa;
                        font-size: 24rpx;
                        color: #aaaaaa;
                        display: flex;
                        justify-content: center;
                        align-items: center;
                    }
                    .active {
                        background: #238f6b;
                        color: #fff;
                    }
                }
            }
            .list {
                ul {
                    display: flex;
                }
                li {
                    flex: 1;
                    text-align: center;
                    height: 158rpx;
                    display: flex;
                    /*设置为flex布局*/
                    justify-content: center;
                    align-items: center;
                    /*垂直居中*/
                    p {
                        font-size: 32rpx;
                        line-height: 42rpx;
                        color: #333333;
                    }
                    h4 {
                        font-size: 28rpx;
                        line-height: 54rpx;
                        color: #aaaaaa;
                    }
                }
            }
        }
        .record_wrap {
            height: 100%;
            background: #fff;
            .list {
                height: 118rpx;
                border-bottom: 1px solid #eee;
                display: flex;
                align-items: center;
                justify-content: space-between;
                .l {
                    font-size: 28rpx;
                    color: #333333;
                    display: flex;
                    padding-left: 43rpx;
                    .ll {
                        margin-right: 20rpx;
                        img {
                            width: 28rpx;
                            height: 24rpx;
                        }
                    }
                }
                .r {
                    img {
                        width: 18rpx;
                        height: 28rpx;
                        margin-right: 20rpx;
                    }
                }
            }
        }
        /*帖子*/
        .post_wrap {
            background: #edf1f4;
            .title {
                width: 100%;
                height: 100rpx;
                background-color: #ffffff;
                border-bottom: 1px solid #edf1f4;
                .list {
                    display: flex;
                    justify-content: center;
                    align-items: center;
                    li {
                        height: 100rpx;
                        .biaoti {
                            font-size: 28rpx;
                            color: #333333;
                            line-height: 98rpx;
                            border-bottom: 1px solid #238f6b;
                        }
                    }
                }
            }
            .posts {
                margin-bottom: 20rpx;
                background: #fff;
                .user_wrap {
                    padding: 20rpx 45rpx 0rpx 30rpx;
                    display: flex;
                    align-items: center;
                    justify-content: space-between;
                    background: #fff; // border: 1px solid #eee;
                    .l {
                        display: flex;
                        flex-direction: column;
                        .user_box {
                            display: flex;
                            .title_img {
                                width: 100rpx;
                                height: 100rpx;
                                border-radius: 50%;
                                border: 1px solid red;
                                img {
                                    width: 100rpx;
                                    height: 100rpx;
                                }
                            }
                            .text {
                                margin-left: 27rpx;
                                padding-top: 15rpx;
                                display: flex;
                                flex-direction: column;
                                .name {
                                    font-size: 28rpx;
                                    line-height: 36rpx;
                                    color: #333333;
                                }
                                .fans {
                                    font-size: 24rpx;
                                    line-height: 46rpx;
                                    color: #aaaaaa;
                                }
                            }
                        }
                    }
                    .r {
                        .block {
                            display: flex;
                            justify-content: center;
                            align-items: center;
                            padding: 10rpx 0;
                            img {
                                width: 49rpx;
                                height: 10rpx;
                            }
                        }
                    }
                }
                .text_wrap {
                    padding: 0rpx 45rpx 20rpx 30rpx;
                    .text {
                        font-size: 28rpx;
                        color: #333333;
                        .point {
                            font-size: 28rpx;
                            color: #e67549;
                        }
                    }
                }
                .pic_wrap {
                    padding: 0rpx 45rpx 20rpx 30rpx;
                    ul {
                        display: flex;
                        .item {
                            flex: 1;
                            text-align: center;
                            img {
                                width: 220rpx;
                                height: 220rpx;
                            }
                        }
                    }
                }
                .review_wrap {
                    padding: 20rpx 45rpx 20rpx 30rpx;
                    border-top: 1px solid #eee;
                    .hudong {
                        display: flex;
                        .item {
                            flex: 1;
                            justify-content: center;
                            font-size: 28rpx;
                            text-align: center;
                            color: #666666;
                            align-items: center;
                            display: flex;
                            img {
                                width: 28rpx;
                                height: 26rpx;
                                margin-right: 10rpx;
                            }
                        }
                    }
                }
            }
        }
    }
}
</style>
