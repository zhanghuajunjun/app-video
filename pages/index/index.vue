<template>
    <view>
        <swiper class="swiper" :indicator-dots="indicatorDots" :autoplay="autoplay" :circular="circular"
            :indicator-active-color="indicatorActiveColor">
            <swiper-item v-for="(item,index) in imgUrls" :key="index">
                <image :src="item.image" mode="" class="img"></image>
            </swiper-item>
        </swiper>
        <view class="box">
            <view class="hot_nav">
                <image src="../../static/image/hot.png" class="hot_img"></image>
                <view class="">
                    热门超英
                </view>
            </view>
            <view>
                <scroll-view scroll-x="true" show-scrollbar="true">
                    <view class="scroll-view_H">
                        <view v-for="(item,index) in superhero" :key="index" class="scroll_item">
                            <image :src="item.cover" class="item_cover" @click="goDetail(item)"></image>
                            <view class="item_name">
                                {{item.name}}
                            </view>
                            <view class="score">
                                <uni-rate :readonly="true" :value="item.score/2" :max="5" allow-half :size="size" class="rate" />
                                {{item.score}}
                            </view>
                        </view>
                    </view>
                </scroll-view>
            </view>
            <view class="hot_nav">
                <image src="../../static/image/notice.png" class="hot_img"></image>
                <view class="">
                    热门预告
                </view>
            </view>
            <view class="notice">
                <view v-for="(item,index) in trailer" :key="index" class="notice_item" :class="{'active':(index+1)%2==0}">
                    <video :src="item.trailer" controls :poster="item.cover" :id="item.id" @play='handle(item.id)'
                        class="myVideo"></video>
                </view>
            </view>
            <view class="guessULike" v-for="(item,index) in guessULike" :key="index">
                <view class="guessULike_left" @click="goDetail(item)">
                    <view class="img_box">
                        <image :src="item.cover" class="guessULike_img"></image>
                    </view>
                    <view>
                        <view>{{item.name}}</view>
                        <uni-rate :readonly="true" :value="item.score/2" :max="5" allow-half :size="size" class="guessULike_rate" />
                        <view class="basicInfo">{{item.basicInfo}}</view>
                        <view class="releaseDate">{{item.releaseDate}}</view>
                    </view>
                </view>
                <view class="guessULike_right">
                    <view class="zan" @click="zan">
                        <image src="../../static/image/zan.png" class="zan_img"></image>
                        <view>赞一下</view>
                    </view>
                </view>
            </view>
        </view>
    </view>
</template>

<script>
    export default {
        name: "",
        components: {

        },
        props: {},
        data() {
            return {
                imgUrls: [],
                indicatorDots: true,
                autoplay: true,
                circular: true,
                indicatorActiveColor: '#eee',
                superhero: [],
                trailer: [],
                guessULike: [],
                video: ''
            }
        },
        methods: {
            zan() {
                uni.showToast({
                    title: '点赞成功！',
                    duration: 2000
                });
            },
            handle(id) {
                this.videoContent = uni.createVideoContext(id)
                let trailer = this.trailer
                trailer.map(item => {
                    if (item.id !== id) {
                        uni.createVideoContext(item.id).pause();
                    }
                })
            },
            goDetail(item) {
                uni.navigateTo({
                    url: `/pages/detail/detail?item=${JSON.stringify(item)}`
                })
            }
        },
        mounted() {

        },
        onPullDownRefresh() {
            setTimeout(() => {
                uni.stopPullDownRefresh();
                uni.showToast({
                    title: '刷新成功！',
                    duration: 1000,
                    icon: 'none'
                });
            }, 1000);
            uni.request({
                url: `${this.$api}/index/guessULike?qq=1844532477`,
                method: 'POST',
                data: {},
                success: res => {
                    res.data.data.map(item => {
                        item.releaseDate = item.releaseDate.substr(0, item.releaseDate.indexOf("("));
                    })
                    this.guessULike = res.data.data
                },
            });
        },
        onLoad() {
            uni.showLoading({
                title: '加载中'
            });
            // 轮播图
            uni.request({
                url: `${this.$api}/index/carousel/list?qq=1844532477`,
                method: 'POST',
                data: {},
                success: res => {
                    console.log(res);
                    this.imgUrls = res.data.data
                },
            });
            // 热门超英
            uni.request({
                url: `${this.$api}/index/movie/hot?qq=1844532477&type=superhero`,
                method: 'POST',
                data: {},
                success: res => {
                    this.superhero = res.data.data
                },
            });
            // 热门预告
            uni.request({
                url: `${this.$api}/index/movie/hot?qq=1844532477&type=trailer`,
                method: 'POST',
                data: {},
                success: res => {
                    this.trailer = res.data.data
                },
            });
            // 猜你喜欢
            uni.request({
                url: `${this.$api}/index/guessULike?qq=1844532477`,
                method: 'POST',
                data: {},
                success: res => {
                    uni.hideLoading();
                    res.data.data.map(item => {
                        item.releaseDate = item.releaseDate.substr(0, item.releaseDate.indexOf("("));
                    })
                    this.guessULike = res.data.data
                },
            });
            setTimeout(function() {
                uni.hideLoading();
            }, 2000);
        },
        filters: {

        },
        computed: {
            size() {
                let size = 0
                uni.getSystemInfo({
                    success: (res) => {
                        size = (res.windowWidth / 375) * 12
                    }
                });
                return size
            }
        },
        watch: {

        },
        directives: {

        }
    }
</script>

<style scoped lang="scss">
    .box {
        padding: 20rpx;
    }

    .swiper {
        width: 750rpx;
        height: 400rpx;
    }

    .img {
        width: 750rpx;
        height: 400rpx;
    }

    .hot_nav {
        display: flex;
        margin-bottom: 20rpx;
        font-size: 32rpx;
        align-items: center;
    }

    .hot_img {
        width: 50rpx;
        height: 50rpx;
        margin-right: 20rpx;
    }

    .scroll-view_H {
        display: flex;
        margin-bottom: 20rpx;
    }

    .scroll_item {
        margin-left: 20rpx;
    }

    .scroll_item:first-child {
        margin-left: 0;
    }

    .item_cover {
        width: 152rpx;
        height: 150rpx;
    }

    .item_name {
        font-size: 28rpx;
        width: 152rpx;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        margin: 4rpx 0;
    }

    .rate {
        width: 115rpx;
    }

    .score {
        width: 152rpx;
        font-size: 28rpx;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .notice {
        display: flex;
        flex-wrap: wrap;
    }

    .notice_item {
        width: 345rpx;
        height: 300rpx;
        margin-bottom: 20rpx;
    }

    .active {
        margin-left: 20rpx;
    }

    .myVideo {
        width: 345rpx;
        height: 300rpx;
    }

    .guessULike {
        margin-top: 20rpx;
        display: flex;
        align-items: center;
        width: 710rpx;
    }

    .guessULike_left {
        width: 552rpx;
        display: flex;
        height: 280rpx;
    }

    .img_box {
        width: 180rpx;
        height: 280rpx;
        margin-right: 20rpx;
    }

    .guessULike_img {
        width: 180rpx;
        height: 280rpx;
        border-radius: 10rpx;
    }

    .guessULike_rate {
        width: 350rpx;
        margin: 10rpx 0;
    }

    .basicInfo {
        width: 350rpx;
        font-size: 28rpx;
        color: #666;
        margin-bottom: 10rpx;
    }

    .releaseDate {
        width: 350rpx;
        font-size: 28rpx;
        color: #666;
    }

    .guessULike_right {
        width: 138rpx;
        margin-left: 20rpx;
        height: 280rpx;
        display: flex;
        justify-content: center;
        align-items: center;
        border-left: 2rpx dashed #666666;
    }

    .zan {
        color: #f4ea2a;
        font-size: 28rpx;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    .zan_img {
        width: 30rpx;
        height: 30rpx;
        margin-bottom: 10rpx;
    }
</style>
