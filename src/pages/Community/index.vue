<template>
    <div class="package">
        <div class="search">
            <div class="l">
                <img src="../../../static/images/search.png" alt>
                <input type="text" value="点击输入你要查找的内容">
            </div>
            <div class="r" @click="postMessage">
                <img src="../../../static/images/edit.png" alt>发帖
            </div>
        </div>
        <div class="tab">
            <ul class="list">
                <li class="item" :class="current == 1 ? 'active': ''" @click="getToggle(1)">关注</li>
                <li class="item" :class="current == 2 ? 'active': ''" @click="getToggle(2)">最新</li>
                <li class="item" :class="current == 3 ? 'active': ''" @click="getToggle(3)">推荐</li>
                <li class="item" :class="current == 4 ? 'active': ''" @click="getToggle(4)">本地</li>
            </ul>
        </div>
        <div class="post_wrap">
            <!-- 暂无内容 -->
            <block v-if="isShow">
                <div class="not_have">
                    <div class="img">
                        <img src="../../../static/images/none.png" alt>
                    </div>
                    <div class="text">暂无内容</div>
                </div>
            </block>
            <!-- 暂无内容 -->
            <block v-else>
                <div class="posts" v-for="(item, index) in articlesList" :key="index">
                    <div class="user_wrap">
                        <div class="l">
                            <div class="user_box">
                                <div class="title_img">
                                    <img :src="item.clientHeadPortrait" alt>
                                </div>
                                <div class="text">
                                    <div class="name">{{item.clientName}}</div>
                                    <div class="fans">{{item.addTime}}</div>
                                </div>
                            </div>
                        </div>
                        <!-- <block> -->
                        <div class="r">
                            <div class="add_btn" v-if="item.followStatus == 1" @click="getFollowStatus(item.clientId, 1)">
                                <img src="../../../static/images/add.png" alt>关注
                            </div>
                            <div class="followed_btn" v-else-if="item.followStatus == 2" @click="getFollowStatus(item.clientId, 2)">
                                <img src="../../../static/images/ok.png" alt>已关注
                            </div>
                            <div class="followed_btn" v-else-if="item.followStatus == 3" @click="getFollowStatus(item.clientId, 2)">
                                互相关注
                            </div>
                        </div>
                        <!-- </block> -->
                    </div>
                    <div class="text_wrap">
                        <div>
                            <span class="text">
                                <block v-if="point">
                                </block>
                                <block v-else>
                                      <span class="point">- {{item.keyword}} -</span>
            </block>
            {{item.content}}
            <span class="point" @click="seeAll(item.id)">全文</span>
            </span>
            </div>
            </div>
            <div class="pic_wrap" @click="seeAll(item.id)">
                <ul>
                    <li class="item" v-for="(imgItem, imgIndex) in item.imgList" :key="imgIndex">
                        <img :src="imgHttp + '/' + imgItem.url" alt>
                    </li>
                </ul>
            </div>
            <div class="review_wrap">
                <ul class="hudong">
                    <li class="item">
                        <img src="../../../static/images/share.png" alt>
                        <span>转发</span>
                    </li>
                    <li class="item">
                        <img src="../../../static/images/pl.png" alt>
                        <span>{{item.evaluateNum}}</span>
                    </li>
                    <block v-if="item.isZan == 0">
                        <li class="item" @click="getZanStatus(item.id, 1)">
                            <img src="../../../static/images/dz.png" alt>
                            <span>{{item.zanNum}}</span>
                        </li>
                    </block>
                    <block v-else-if="item.isZan == 1">
                        <li class="item" @click="getZanStatus(item.id, 2)">
                            <img src="../../../static/images/dz_active.png" alt>
                            <span>{{item.zanNum}}</span>
                        </li>
                    </block>
                </ul>
            </div>
            </div>
            </block>
        </div>
    </div>
</template>

<script>
import { md5, get, host, key, img } from "../../utils/api.js";
const Base64 = require('js-base64').Base64;
// decode
export default {
    data() {
        return {
            clientId: Number,
            keyword: '',
            page: 1,
            pagerow: 30,
            type: Number,
            current: 2,
            isShow: false,
            articlesList: [],
            imgHttp: '',
            point: false,
            toast: ''
        };
    },
    onShow() {
        this.clientId = wx.getStorageSync('clientId');
        this.imgHttp = img
        this.getArticlesList(this.current)
        console.log(this.point);
    },
    onHide() {},
    onShareAppMessage: function() {},
    methods: {
        // 1.关注 2.最新 3.推荐 4.本地 5.我发布的 6.我收藏的 7.浏览记录
        async getArticlesList(type) {
            let dataList = []
            let sign = md5("clientId=" + this.clientId + "&keyword=" + this.keyword + "&page=" + this.page + "&pagerow=" + this.pagerow + "&type=" + type + "&key=" + key);
            const articlesData = await get("/api/client/articles", {
                clientId: this.clientId,
                keyword: this.keyword,
                page: this.page,
                pagerow: this.pagerow,
                type,
                sign
            });
            console.log('帖子列表页-->', articlesData);
            if (articlesData.result.code === "200") {
                if (articlesData.data == [] || articlesData.data.length == 0) {
                    this.isShow = true
                } else {
                    this.isShow = false
                    dataList = articlesData.data;
                    for (var i = 0; i < dataList.length; i++) {
                        dataList[i].clientName = Base64.decode(dataList[i].clientName)
                        dataList[i].addTime = this.getTime(dataList[i].addTime)
                        if (dataList[i].keyword != null || dataList[i].keyword != "") {
                            this.point = true
                        } else {
                            this.point = false
                        }
                    }
                    this.articlesList = dataList
                }

            } else {
                console.log("获取列表失败");
            }
        },
        async getFollow(followClientId, type) {
            let sign = md5("clientId=" + this.clientId + "&followClientId=" + followClientId + "&type=" + type + "&key=" + key);
            const followData = await get("/api/client/follow", {
                clientId: this.clientId,
                followClientId,
                type,
                sign
            });
            console.log('关注状态-->', followData);
            if (followData.result.code === "200") {
                wx.showToast({
                    title: this.toast,
                    icon: 'success',
                    duration: 1500,
                    mask: true
                });
                this.getArticlesList(this.current)
            } else {

            }
        },
        getFollowStatus(followClientId, type) {
            if (type == 1) {
                this.toast = '关注成功'
            } else if (type == 2) {
                this.toast = '已取消关注'
            }
            this.getFollow(followClientId, type)
        },
        async getZan(artId, type, toast) {
            let sign = md5("artId=" + artId + "&clientId=" + this.clientId + "&type=" + type + "&key=" + key);
            const followData = await get("/api/client/zan", {
                artId,
                clientId: this.clientId,
                type,
                sign
            });
            console.log('关注状态-->', followData);
            if (followData.result.code === "200") {
                this.getArticlesList(this.current)
            } else {

            }
        },
        getZanStatus(artId, type) {
            this.getZan(artId, type)
        },
        getTime(unixtime) {
            let dateTime = new Date(parseInt(unixtime))
            let year = dateTime.getFullYear();
            let month = dateTime.getMonth() + 1;
            let day = dateTime.getDate();
            let hour = dateTime.getHours();
            let minute = dateTime.getMinutes();
            let second = dateTime.getSeconds();
            let now = new Date();
            let now_new = Date.parse(now.toDateString()); //typescript转换写法
            let milliseconds = now_new - dateTime;
            let timeSpanStr = year + '年' + month + '月' + day + '日' + ' ' + hour + ':' + minute;
            return timeSpanStr;
        },
        getToggle(flag) {
            if (this.current === flag) {
                return false
            } else {
                this.current = flag
                this.getArticlesList(this.current)
            }
        },
        seeAll(artId) {
            //查看全文
            wx.navigateTo({
                url: "../packageB/CommunityInfo/main?artId=" + artId
            });
        },
        postMessage() {
            wx.navigateTo({
                url: "../packageB/PostMessage/main"
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
    position: relative;
    z-index: 100;
    /*搜索*/
    .search {
        width: 100%;
        height: 105rpx;
        padding: 21rpx 32rpx 0 25rpx;
        display: flex;
        align-items: center;
        background: #fff;
        .l {
            position: relative;
            img {
                width: 40rpx;
                height: 40rpx;
                position: absolute;
                left: 32rpx;
                top: 20rpx;
            }
            input {
                width: 488rpx;
                height: 84rpx;
                padding-left: 72rpx;
                border: 1rpx solid #eeeeee;
                border-radius: 42rpx;
                font-size: 28rpx;
                background: #f5f5f5;
                color: #aaaaaa;
            }
        }
        .r {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-left: 33rpx;
            img {
                width: 30rpx;
                height: 30rpx;
                margin-right: 10rpx;
            }
            font-size: 28rpx;
            color: #e67549;
        }
    }
    .tab {
        width: 100%;
        height: 85rpx;
        margin-bottom: 20rpx;
        background: #fff;
        .list {
            width: 100%;
            height: 85rpx;
            display: flex;
            .item {
                flex: 1;
                justify-content: center;
                font-size: 28rpx;
                text-align: center;
                color: #666666;
                align-items: center;
                display: flex;
            }
            .item.active {
                border-bottom: 3px solid #333;
            }
        }
    }
    /*帖子*/
    .post_wrap {
        background: #edf1f4;
        height: 100%;
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
        .not_have {
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-direction: column;
            .img {
                width: 430rpx;
                height: 408rpx;
                img {
                    width: 430rpx;
                    height: 408rpx;
                }
            }
            .text {
                font-size: 32rpx;
                line-height: 36rpx;
                color: #aaaaaa;
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
                        // flex: 1;
                        text-align: center;
                        width: 33.3333%;
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
                            height: 28rpx;
                            margin-right: 10rpx;
                        }
                    }
                }
            }
        }
    }
}
</style>
