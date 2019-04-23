<template>
    <div class="home">
        <!-- 选择作物模块 -->
        <div class="weather" @click="getLogin">
            <div class="number">
                <div class="address">{{province}}&nbsp;&nbsp;{{city}}</div>
                <div class="temperature">{{temperature}}℃</div>
            </div>
            <div class="wearher_img">
                <img :src="images" alt mode="aspectFit">
            </div>
        </div>
        <div class="header">
            <div class="title">
                您种植的作物
                <span>请至少选择一种作物</span>
            </div>
            <div class="add">
                <ul class="list">
                    <li class="item" @longtap.stop="longtap" v-for="(item, index) in CropsList" :key="index">
                        <img :src="img + item.logo" alt>
                        <div class="item_remove" :class="isRemove" @click="remove(item.id)">
                            <img src="../../../static/images/remove.png" alt>
                        </div>
                    </li>
                    <block v-if="zero">
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                    </block>
                    <block v-if="one">
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                    </block>
                    <block v-if="two">
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                    </block>
                    <block v-if="three">
                        <li class="item" @click="add">
                            <i class="iconfont icon-jiahao"></i>
                        </li>
                    </block>
                    <block v-if="four"></block>
                </ul>
            </div>
        </div>
        <div class="database">
            <div class="title">病虫害知识</div>
            <div class="content">
                <ul class="list">
                    <!-- <li class="item" v-for="(item,index) in list" :key="index" @click='details(item.cropId, item.id)'>
                <div class="item_img">
                  <img :src="img + item.img" alt="" mode="scaleToFill">
                </div>
                <div class="title">{{item.diseasesName}}</div>
              </li>-->
                    <li class="item" v-for="(item,index) in list" :key="index" @click="details(item.cropId, item.id)">
                        <div class="item_box">
                            <div class="item_l">
                                <img :src="img + item.img" alt mode="scaleToFill">
                            </div>
                            <div class="item_r">
                                <h4>{{item.diseasesName}}</h4>
                                <p v-if="item.summary != null">{{item.summary}}</p>
                                <p v-if="item.summary == null">暂无介绍</p>
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
        <div class="photograph" @click="Photo">
            <img :src="photo" alt>
            <!-- <img src="../../../static/images/paizhao.png" alt> -->
        </div>
        <canvas style="width: 300px; height: 200px;" canvas-id="myCanvas"></canvas>
    </div>
</template>

<script>
import { md5, key, get, host, FillesPath, img } from "../../utils/api.js";
import { AMapWX } from "../../utils/amap-wx.js";
export default {
    data() {
        return {
            list: [],
            clientId: Number,
            code: "",
            img: "",
            CropsList: [],
            isRemove: "hide",
            markersData: {
                latitude: "", //纬度
                longitude: "", //经度
                key: "b1d96a5744d45e45c36a16d35e64315f" //申请的高德地图key
            },
            weather: {},
            city: "",
            province: "",
            temperature: "",
            images: null,
            zero: false,
            one: false,
            two: false,
            three: false,
            four: false,
            // photo:'../../../static/images/paizhao.png',
            photo: "/../../../static/images/paizhao.png",
            predictData: [],
            predictId: [],
            labelIds: "",
            labelNnumber: []
        };
    },
    created() {
        this.img = img;
        console.log('this.img : ', this.img);
        this.loadInfo();
    },
    onShow() {
        this.getLogin();
        // this.getDetailsList() 暂时不调用
    },
    onHide() {
        this.predictData = []
        this.predictId = []
        this.labelIds = ''
        this.labelNnumber = []
    },
    onShareAppMessage: function() {},
    methods: {
        async getDetailsList() {
            wx.showLoading({
                title: "加载中"
            });
            let sign = md5(
                "clientId=" +
                this.clientId +
                "&cropId=" +
                this.cropId +
                "&plantId=" +
                this.plantId +
                "&key=" +
                key
            );
            const DetailsList = await get("/api/plantinfo", {
                clientId: this.clientId,
                cropId: this.cropId,
                plantId: this.plantId,
                sign
            });
            if (DetailsList.result.code === "200") {
                wx.hideLoading();
                this.list = DetailsList.data;
                for (var i = 0; i < this.list.length; i++) {
                    if (this.list[i].tag === "img") {
                        this.images = this.list[i].content;
                    }
                    if (this.list[i].tag === "title") {
                        wx.setNavigationBarTitle({
                            title: this.list[i].content
                        });
                    }
                }
            }
        },
        loadInfo: function() {
            var that = this;
            wx.getLocation({
                type: "gcj02", //返回可以用于wx.openLocation的经纬度
                success: function(res) {
                    var latitude = res.latitude; //维度
                    var longitude = res.longitude; //经度
                    that.loadCity(latitude, longitude);
                }
            });
        },
        loadCity: function(latitude, longitude) {
            var that = this;
            var myAmapFun = new AMapWX({
                key: that.markersData.key
            });
            myAmapFun.getRegeo({
                location: "" + longitude + "," + latitude + "",
                success: function(data) {},
                fail: function(info) {}
            });
            myAmapFun.getWeather({
                success: function(data) {
                    that.weather = data;
                    that.province = that.weather.liveData.province;
                    that.city = that.weather.liveData.city;
                    that.temperature = that.weather.temperature.data;
                    if (that.weather.weather.data === "晴") {
                        that.images = require("../../../static/images/晴天.png");
                    } else if (that.weather.weather.data === "多云") {
                        that.images = require("../../../static/images/多云.png");
                    } else if (that.weather.weather.data === "阴") {
                        that.images = require("../../../static/images/阴天.png");
                    } else if (that.weather.weather.data === "阵雨") {
                        that.images = require("../../../static/images/阵雨.png");
                    } else if (that.weather.weather.data === "雷阵雨") {
                        that.images = require("../../../static/images/雷阵雨.png");
                    } else if (that.weather.weather.data === "雨夹雪") {
                        that.images = require("../../../static/images/雨夹雪.png");
                    } else if (that.weather.weather.data === "小雨") {
                        that.images = require("../../../static/images/小雨.png");
                    } else if (that.weather.weather.data === "中雨") {
                        that.images = require("../../../static/images/中雨.png");
                    } else if (that.weather.weather.data === "大雨") {
                        that.images = require("../../../static/images/大雨.png");
                    } else if (that.weather.weather.data === "暴雨") {
                        that.images = require("../../../static/images/暴雨.png");
                    } else if (that.weather.weather.data === "大暴雨") {
                        that.images = require("../../../static/images/暴雨.png");
                    } else if (that.weather.weather.data === "特大暴雨") {
                        that.images = require("../../../static/images/暴雨.png");
                    } else if (that.weather.weather.data === "阵雪") {
                        that.images = require("../../../static/images/小雪.png");
                    } else if (that.weather.weather.data === "小雪") {
                        that.images = require("../../../static/images/小雪.png");
                    } else if (that.weather.weather.data === "中雪") {
                        that.images = require("../../../static/images/中雪.png");
                    } else if (that.weather.weather.data === "大雪") {
                        that.images = require("../../../static/images/大雪.png");
                    } else if (that.weather.weather.data === "暴雪") {
                        that.images = require("../../../static/images/暴雪.png");
                    } else if (that.weather.weather.data === "雾") {
                        that.images = require("../../../static/images/雾.png");
                    } else if (that.weather.weather.data === "沙尘暴") {
                        that.images = require("../../../static/images/扬沙.png");
                    } else if (that.weather.weather.data === "扬沙") {
                        that.images = require("../../../static/images/扬沙.png");
                    } else if (that.weather.weather.data === "雾霾") {
                        that.images = require("../../../static/images/雾霾.png");
                    }
                },
                fail: function(info) {}
            });
        },
        async getLoginCode(code) {
            let sign = md5("code=" + code + "&key=" + key);
            const data = await get("/api/login", {
                code: code,
                sign: sign
            });

            if (data.result.code === "200") {
                this.clientId = data.data.clientId;
                wx.setStorageSync("clientId", this.clientId);
                this.getCrops(this.clientId);
                this.getList(this.clientId);
            } else {}
        },
        getLogin() {
            var that = this;
            wx.login({
                success(res) {
                    if (res.code) {
                        that.code = res.code;
                        that.getLoginCode(that.code);
                    } else {}
                }
            });
        },
        async getCrops(id) {
            this.clientId = id;
            let sign = md5("clientId=" + this.clientId + "&key=" + key);
            const ListData = await get("/api/mycrop", {
                clientId: this.clientId,
                sign
            });
            console.log("ListData", ListData)
            if (ListData.data.length === 0) {
                this.zero = true;
                this.one = false;
                this.two = false;
                this.three = false;
                this.four = false;
            } else if (ListData.data.length === 1) {
                this.zero = false;
                this.one = true;
                this.two = false;
                this.three = false;
                this.four = false;
            } else if (ListData.data.length === 2) {
                this.zero = false;
                this.one = false;
                this.two = true;
                this.three = false;
                this.four = false;
            } else if (ListData.data.length === 3) {
                this.zero = false;
                this.one = false;
                this.two = false;
                this.three = true;
                this.four = false;
            } else if (ListData.data.length === 4) {
                this.zero = false;
                this.one = false;
                this.two = false;
                this.three = false;
                this.four = true;
            }
            this.CropsList = ListData.data;
        },
        longtap() {
            this.isRemove = "show";
        },
        async remove(id) {
            this.clientId = wx.getStorageSync("clientId");
            let sign = md5(
                "clientId=" + this.clientId + "&cropId=" + id + "&key=" + key
            );
            const ListData = await get("/api/delcrop", {
                clientId: this.clientId,
                cropId: id,
                sign
            });
            if (ListData.result.code === "200") {
                this.getCrops(this.clientId);
                this.isRemove = "hide";
            }
        },
        async getList(id) { //病虫害知识
            this.clientId = id;
            let sign = md5("clientId=" + this.clientId + "&key=" + key);
            const ListData = await get("/api/recomplant", {
                clientId: this.clientId,
                sign
            });
            this.list = ListData.data;
            console.log('this.list: ', this.list);
        },
        add() {
            wx.navigateTo({
                url: "/pages/AddCrops/main"
            });
        },
        details(cropId, id) {
            wx.navigateTo({
                url: "/pages/Details/main?cropId=" + cropId + "&plantId=" + id
            });
        },
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
        Photo() {
            const ctx = wx.createCanvasContext('myCanvas')
            var that = this
            wx.chooseImage({
                count: 1,
                sizeType: ['original', 'compressed'],
                sourceType: ['album', 'camera'],
                success(res) {
                    const firstTempFiles = res.tempFilePaths[0]
                    wx.getImageInfo({
                        src: firstTempFiles,
                        success(res) {
                            var canvasWidth = res.width;
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
                                                    wx.setStorageSync('labelNnumber', that.labelNnumber)
                                                    that.labelIds = that.predictId.join(',')
                                                    wx.hideLoading()
                                                    wx.showToast({
                                                        title: '上传成功',
                                                        icon: 'success',
                                                        duration: 3000,
                                                        success(res) {
                                                            wx.navigateTo({
                                                                url: '/pages/ShareResults/main?labelIds=' + that.labelIds + '&tempFiles=' + firstTempFiles + '&probability=' + probability
                                                            })
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
        }
    }
};
</script>

<style scoped lang="scss">
page {
    background: #edf1f4;
}

.home {
    display: flex;
    flex-direction: column;
    width: 100%;
    background: #edf1f4;
    position: relative;
    z-index: 100;
    .weather {
        margin-bottom: 20rpx;
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        align-items: center;
        background: #fff;
        padding: 20rpx 80rpx;
        .number {
            display: flex;
            flex-direction: column;
            .address {
                font-size: 35rpx;
                color: #000;
                font-weight: 550;
            }
            .temperature {
                font-size: 60rpx;
                color: #000;
                font-weight: 700;
            }
        }
        .wearher_img {
            width: 120rpx;
            height: 120rpx;
            display: flex;
            justify-content: center;
            align-items: center;
            img {
                width: 120rpx;
                height: 120rpx;
                display: block;
            }
        }
    }
    .header {
        display: flex;
        background: #fff;
        flex-direction: column;
        margin-bottom: 20rpx;
        .title {
            padding: 20rpx 50rpx;
            font-size: 28rpx;
            color: #4c4c4c;
            span {
                font-size: 24rpx;
                margin-left: 30rpx;
                color: #666;
            }
        }
        .add {
            width: 100%;
            height: 100%;
            box-shadow: 0 10rpx 10rpx #ececec;
            .list {
                padding: 0rpx 60rpx 20rpx 60rpx;
                padding: 20rpx 30rpx;
                display: flex;
                flex-direction: row;
                justify-content: space-between;
                overflow: hidden;
                .item {
                    width: 130rpx;
                    height: 130rpx;
                    background: #ececec;
                    display: flex;
                    justify-content: center;
                    align-items: center;
                    border-radius: 50%;
                    position: relative;
                    i {
                        color: #508f00;
                        font-size: 40rpx;
                    }
                    img {
                        width: 130rpx;
                        height: 130rpx;
                        display: block;
                        border-radius: 50%;
                    }
                    .item_remove {
                        width: 50rpx;
                        height: 50rpx;
                        position: absolute;
                        top: 0;
                        right: -10rpx;
                        display: flex;
                        justify-content: center;
                        align-items: center;
                        img {
                            width: 50rpx;
                            height: 50rpx;
                            display: block;
                        }
                    }
                    .show {
                        display: block;
                    }
                    .hide {
                        display: none;
                    }
                }
            }
        }
    }
    .database {
        display: flex;
        flex-direction: column;
        background: #fff;
        .title {
            padding: 40rpx 50rpx;
            font-size: 28rpx;
            color: #4c4c4c;
        }
        .content {
            width: 100%;
            display: flex;
            flex-direction: column;
            .list {
                width: 100%;
                display: flex;
                flex-direction: column;
                .item {
                    padding: 0rpx 50rpx 28rpx 50rpx;
                    .item_box {
                        overflow: hidden;
                        border-bottom: 1px solid #ddd;
                        padding-bottom: 20rpx;
                    }
                    .item_l {
                        width: 35%;
                        float: left;
                        img {
                            width: 200rpx;
                            height: 150rpx;
                            border-radius: 10rpx;
                        }
                    }
                    .item_r {
                        width: 65%;
                        float: right;
                        h4 {
                            font-size: 32rpx;
                            color: #333;
                            line-height: 60rpx;
                        }
                        p {
                            font-size: 28rpx;
                            color: #aaa;
                            display: -webkit-box;
                            -webkit-box-orient: vertical;
                            -webkit-line-clamp: 2;
                            overflow: hidden;
                        }
                    }
                } // .item {
                //   width: 100%;
                //   display: flex;
                //   flex-direction: column;
                //   .item_img {
                //     width: 100%;
                //     height: 305rpx;
                //     display: flex;
                //     justify-content: center;
                //     align-items: center;
                //     background: #fff;
                //     img {
                //       display: block;
                //       max-width: 100%;
                //       height: 100%;
                //       width: 90%;
                //     }
                //   }
                //   .title {
                //     padding: 20rpx 40rpx 10rpx 40rpx;
                //     font-size: 26rpx;
                //     color: #606060;
                //   }
                //   .date {
                //     padding: 10rpx 40rpx 20rpx 40rpx;
                //     font-size: 26rpx;
                //     color: #939393;
                //   }
                // }
            }
        }
    }
    /* 拍照*/
    .photograph {
        width: 145rpx;
        height: 145rpx;
        border-radius: 50%;
        position: fixed;
        z-index: 1000;
        bottom: 72rpx;
        right: 21rpx;
        img {
            width: 145rpx;
            height: 145rpx;
            border-radius: 50%;
        }
    }
    canvas {
        position: relative;
        top: -999rpx;
        left: -999rpx;
        z-index: 222;
    }
}
</style>
