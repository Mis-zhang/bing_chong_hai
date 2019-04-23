<template>
    <div class="package">
        <!-- 帖子正文 -->
        <div class="post_wrap">
            <div class="posts">
                <div class="user_wrap">
                    <div class="l">
                        <div class="user_box">
                            <div class="title_img">
                                <img :src="artInfoList.clientHeadPortrait" alt>
                            </div>
                            <div class="text">
                                <div class="name">{{artInfoList.clientName}}</div>
                                <div class="fans">{{artInfoList.addTime}}</div>
                            </div>
                        </div>
                    </div>
                    <div class="r" @click="dotFn">
                        <div class="add_btn" v-if="artInfoList.followStatus == 1">
                            <img src="../../../../static/images/add.png" alt>关注
                        </div>
                        <div class="followed_btn" v-else-if="artInfoList.followStatus == 2">
                            <img src="../../../../static/images/ok.png" alt>已关注
                        </div>
                        <div class="followed_btn" v-else-if="artInfoList.followStatus == 3">
                            相互关注
                        </div>
                    </div>
                </div>
                <div class="text_wrap">
                    <div>
                        <span class="text">
                                      <block  v-if="keywordValue">
                                          <span class="point">- {{artInfoList.keyword}} -</span>
                        </block>
                        {{artInfoList.content}}
                        </span>
                    </div>
                </div>
                <div class="pic_wrap">
                    <ul>
                        <li class="item" v-for="(item, index) in artInfoList.imgList" :key="index">
                            <img :src="imgHttp + '/' + item.url" alt>
                        </li>
                    </ul>
                </div>
                <block v-if="artInfoList.isCollect == 0">
                    <div class="review_wrap">
                        <div class="collect" @click="getCollectStatus(artInfoList.id, 1)">
                            <img src="../../../../static/images/collect.png" alt>收藏
                        </div>
                    </div>
                </block>
                <block v-else-if="artInfoList.isCollect == 1">
                    <div class="review_wrap">
                        <div class="collect" @click="getCollectStatus(artInfoList.id, 2)">
                            <img src="../../../../static/images/collect_active.png" alt>已收藏
                        </div>
                    </div>
                </block>
            </div>
        </div>
        <!-- 用户评论 -->
        <div class="list_wrap">
            <div class="title">
                <div class="pl">评论</div>
                <div class="num">{{artInfoList.evaluateNum}}</div>
            </div>
            <div class="not_content" v-if="artInfoList.evaluateList == []">
                <div class="not_img">
                    <img src="../../../../static/images/none2.png" alt="">
                </div>
                <div class="not_txt">
                    该帖子没有人评论 &nbsp;<span @click="openDiscuss">我要评论</span>
                </div>
            </div>
            <div class="review_list" v-else>
                <div class="content">
                    <ul class="list">
                        <li class="item" v-for="(item, index) in artInfoList.evaluateList" :key="index">
                            <div class="l">
                                <img :src="item.clientHeadPortrait" alt>
                            </div>
                            <div class="r">
                                <div class="name">{{item.clientName}}</div>
                                <div class="cont" @click="getCommentDetails(artInfoList.id, item.clientId, item.id)">{{item.content}}</div>
                                <div class="time">
                                    <div class="time_l">{{item.addTime}}</div>
                                    <div class="time_r" v-if="item.isZan == 0" @click="discussZan(item.id, 1)">
                                        <img src="../../../../static/images/dz.png" alt>
                                        <span>{{item.zanNum}}</span>
                                    </div>
                                    <div class="time_r" v-else-if="item.isZan == 1" @click="discussZan(item.id, 2)">
                                        <img src="../../../../static/images/dz_active.png" alt>
                                        <span>{{item.zanNum}}</span>
                                    </div>
                                </div>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
        <!-- 吸底点赞 -->
        <div class="bottom_pl">
            <ul class="hudong">
                <li class="item">
                    <img src="../../../../static/images/share.png" alt>
                    <span>转发</span>
                </li>
                <li class="item" @click="openDiscuss">
                    <img src="../../../../static/images/community.png" class="teshu" alt>
                    <span>{{artInfoList.evaluateNum}}</span>
                </li>
                <li class="item" v-if="artInfoList.isZan == 0">
                    <img src="../../../../static/images/dz.png" alt>
                    <span>{{artInfoList.zanNum}}</span>
                </li>
                <li class="item" v-else-if="artInfoList.isZan == 1">
                    <img src="../../../../static/images/dz_active.png" alt>
                    <span>{{artInfoList.zanNum}}</span>
                </li>
            </ul>
        </div>
        <div class="discuss" v-if="isDiscussShow">
            <div class="discuss_close" @click.stop="closeDiscuss"></div>
            <div class="discuss_content">
                <div class="discuss_input">
                    <input :value="discussValue" :cursor="cursor" type="text" placeholder="发表评价" placeholder-style="font-size: 24rpx;line-height:60rpx;" @change.stop="getInputValue">
                </div>
                <div class="discuss_btn" @click.stop="addDiscuss(artInfoList.id)">发表</div>
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
            clientId: Number,
            artId: Number,
            imgHttp: '',
            artInfoList: {},
            keywordValue: false,
            discussValue: '',
            cursor: 0,
            isDiscussShow: false
        };
    },
    onShow() {
        this.artId = this.$root.$mp.query.artId;
        this.clientId = wx.getStorageSync('clientId');
        this.imgHttp = img
        this.getArtInfo()
    },
    onHide() {

    },
    onShareAppMessage: function() {},
    methods: {
        async getArtInfo() {
            let sign = md5("artId=" + this.artId + "&clientId=" + this.clientId + "&key=" + key);
            const artInfoData = await get("/api/client/art/info", {
                artId: this.artId,
                clientId: this.clientId,
                sign
            });
            console.log('帖子详情页-->', artInfoData);
            if (artInfoData.result.code === "200") {
                if (artInfoData.data.keyword != "" || artInfoData.data.keyword != null) {
                    this.keywordValue = true
                } else {
                    this.keywordValue = false
                }
                this.artInfoList = artInfoData.data
                this.artInfoList.clientName = Base64.decode(artInfoData.data.clientName)
                this.artInfoList.addTime = this.getTime(artInfoData.data.addTime)
                this.artInfoList.evaluateList.map((item) => {
                    item.clientName = Base64.decode(item.clientName)
                    item.addTime = this.getTime(item.addTime)
                })
            } else {
                console.log("获取列表失败");
            }
        },
        // 收藏接口
        async getCollect(artId, type) {
            let sign = md5("artId=" + artId + "&clientId=" + this.clientId + "&type=" + type + "&key=" + key);
            const collectData = await get("/api/client/collect", {
                artId: artId,
                clientId: this.clientId,
                type,
                sign
            });
            console.log('收藏状态-->', collectData);
            if (collectData.result.code === "200") {
                this.getArtInfo()
            } else {
                console.log("获取列表失败");
            }
        },
        // 改变收藏状态的方法
        getCollectStatus(artId, type) {
            this.getCollect(artId, type)
        },
        // 获取输入框的内容
        getInputValue(e) {
            this.discussValue = e.mp.detail.value
            this.cursor = e.mp.detail.cursor
        },
        // 关闭输入框
        closeDiscuss() {
            this.discussValue = ''
            this.cursor = 0
            this.isDiscussShow = false
        },
        // 打开输入框
        openDiscuss() {
            this.isDiscussShow = true
        },
        //发表评论
        addDiscuss(clientArticleId) {
            if (this.discussValue != '') {
                this.getDiscuss(clientArticleId)
            } else {
                wx.showToast({
                    title: '您还没有输入评论内容',
                    icon: 'none',
                    duration: 1500,
                    mask: true,
                });
            }
        },
        // 评论/回复帖子接口
        async getDiscuss(clientArticleId) {
            let sign = md5("clientArticleId=" + clientArticleId + "&clientId=" + this.clientId + "&content=" + this.discussValue + "&key=" + key);
            const discussData = await get("/api/client/art/evaluate", {
                clientArticleId,
                clientId: this.clientId,
                content: this.discussValue,
                sign
            });
            console.log('评论状态-->', discussData);
            if (discussData.result.code === "200") {
                this.closeDiscuss()
                this.getArtInfo()
            } else {
                console.log("获取列表失败");
            }
        },
        // 点赞/取消点赞
        discussZan(evaId, type) {
          console.log('asdasdas');
          this.getDiscussZan(evaId, type)
        },
        // 点赞/取消点赞接口
        async getDiscussZan(evaId, type) {
            let sign = md5("clientId=" + this.clientId + "&evaId=" + evaId + "&type=" + type + "&key=" + key);
            const discussZanData = await get("/api/client/zaneva", {
                clientId:this.clientId,
                evaId,
                type,
                sign
            });
            console.log('点赞评论状态-->', discussZanData);
            if (discussZanData.result.code === "200") {
              this.getArtInfo()
            } else {
                console.log("获取列表失败");
            }
        },
        //去评论详情页
        getCommentDetails(clientArticleId, clientId, evaluateId) {
          wx.navigateTo({
            url: '../CommentDetails/main?clientArticleId=' + clientArticleId + '&clientId=' + clientId + '&evaluateId=' + evaluateId + '&artId=' + this.artId
          });
        },
        getTime(unixtime) {
            var dateTime = new Date(parseInt(unixtime))
            var year = dateTime.getFullYear();
            var month = dateTime.getMonth() + 1;
            var day = dateTime.getDate();
            var hour = dateTime.getHours();
            var minute = dateTime.getMinutes();
            var second = dateTime.getSeconds();
            var now = new Date();
            var now_new = Date.parse(now.toDateString()); //typescript转换写法
            var milliseconds = now_new - dateTime;
            var timeSpanStr = year + '年' + month + '月' + day + '日' + ' ' + hour + ':' + minute;
            return timeSpanStr;
        },
    }
};
</script>

<style scoped lang="scss">
.package {
    width: 100%;
    min-height: 100%;
    background: #edf1f4;
    position: relative;
    z-index: 100; // margin-bottom: 40rpx;
    /*帖子*/
    .post_wrap {
        background: #edf1f4;
        position: relative;
        margin-bottom: 20rpx;
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
                    text-align: center;
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
                            border: 1px solid #fff;
                            img {
                                width: 100rpx;
                                height: 100rpx;
                                border-radius: 50%;
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
                    .add_btn {
                        width: 120rpx;
                        height: 44rpx;
                        display: flex;
                        justify-content: center;
                        align-items: center;
                        border: solid 1px #238f6b;
                        font-size: 24rpx;
                        color: #238f6b;
                        img {
                            margin-right: 10rpx;
                            width: 16rpx;
                            height: 16rpx;
                        }
                    }
                    .followed_btn {
                        width: 120rpx;
                        height: 44rpx;
                        display: flex;
                        justify-content: center;
                        align-items: center;
                        border: solid 1px #aaaaaa;
                        font-size: 24rpx;
                        color: #aaaaaa;
                        img {
                            margin-right: 10rpx;
                            width: 18rpx;
                            height: 12rpx;
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
                    width: 100%;
                    flex-wrap: wrap;
                    .item {
                        flex: 1;
                        img {
                            width: 220rpx;
                            height: 220rpx;
                        }
                    }
                }
            }
            /*点赞收藏*/
            .review_wrap {
                padding: 20rpx 45rpx 20rpx 30rpx;
                border-top: 1px solid #eee;
                display: flex;
                align-items: center;
                justify-content: flex-end;
                .collect {
                    font-size: 28rpx;
                    color: #aaaaaa;
                    display: flex;
                    align-items: center;
                    justify-content: space-between;
                    img {
                        width: 30rpx;
                        height: 30rpx;
                        margin-right: 10rpx;
                    }
                }
            }
        }
    }
    /*用户评论列表*/
    .list_wrap {
        margin-bottom: 100rpx;
        background: #fff;
        .title {
            width: 100%;
            height: 80rpx;
            padding-left: 34rpx;
            background: #fff;
            display: flex;
            border-bottom: 1px solid #eeeeee;
            .pl {
                font-size: 28rpx;
                color: #333333;
                line-height: 76rpx;
                border-bottom: 8rpx solid #e67549;
            }
            .num {
                margin-left: 10rpx;
                font-size: 28rpx;
                line-height: 80rpx;
                color: #aaaaaa;
            }
        }
        /* 暂无评论*/
        .not_content {
            width: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-direction: column;
            margin-top: 60rpx;
            .not_img {
                width: 182rpx;
                height: 235rpx;
                img {
                    width: 182rpx;
                    height: 235rpx;
                }
            }
            .not_txt {
                font-size: 28rpx;
                line-height: 36rpx;
                color: #aaaaaa;
                span {
                    color: #e67549;
                }
            }
        }
        .review_list {
            background: #fff;
            .content {
                .list {
                    padding: 31rpx 45rpx 20rpx 28rpx;
                    .item {
                        display: flex;
                        width: 100%;
                        /*设置为flex布局*/
                        // justify-content: space-around;
                        padding-bottom: 20rpx;
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
                                display: -webkit-box;
                                -webkit-box-orient: vertical;
                                -webkit-line-clamp: 2;
                                overflow: hidden;
                                font-size: 28rpx;
                                color: #333333;
                                padding-bottom: 10rpx;
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
    }
    /*吸低点赞*/
    .bottom_pl {
        width: 100%;
        background: #fff;
        padding: 20rpx 0rpx 20rpx 0rpx;
        border-top: 1px solid #eee;
        position: fixed;
        bottom: 0;
        left: 0;
        .hudong {
            width: 100%;
            display: flex;
            justify-content: space-between;
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
                    height: 28rpx;
                    margin-right: 10rpx;
                }
                .teshu {
                    width: 42rpx;
                    height: 42rpx;
                }
            }
        }
    }
    .discuss {
        width: 100%;
        height: 100%;
        position: fixed;
        top: 0;
        left: 0;
        z-index: 8888;
        display: flex;
        justify-content: flex-end;
        align-items: flex-end;
        background: rgba(0, 0, 0, .9);
        .discuss_close {
            flex: 1;
            width: 100%;
            height: 93%;
            position: absolute;
            left: 0;
            top: 0;
        }
        .discuss_content {
            width: 700rpx;
            height: 5%;
            padding: 16rpx 25rpx 18rpx 25rpx;
            background: #edf1f4;
            display: flex;
            .discuss_input {
                flex: 1;
                height: 66rpx;
                margin-right: 20rpx;
                background: #ffffff;
                input {
                    width: 98%;
                    height: 60rpx;
                    line-height: 60rpx;
                    font-size: 24rpx;
                    color: #333333;
                }
            }
            .discuss_btn {
                width: 190rpx;
                height: 66rpx;
                background-color: #238f6b;
                border-radius: 4px;
                display: flex;
                justify-content: center;
                align-items: center;
                font-size: 28rpx;
                color: #fff;
            }
        }
    }
}
</style>
