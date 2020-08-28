<template>
    <view>
        <video :src="detailItem.trailer" controls :poster="detailItem.poster" class="myVideo"></video>
        <view class="box">
            <view class="info">
                <image :src="detailItem.cover" class="cover"></image>
                <view class="detail">
                    <view class="name">{{detailItem.name}}</view>
                    <view class="basicInfo">{{detailItem.basicInfo}}</view>
                    <view class="originalName">{{detailItem.originalName}}</view>
                    <view class="releaseDate">{{detailItem.releaseDate}}</view>
                    <view class="rate">
                        <view>
                            <view class="score">综合评分</view>
                            <view class="scoreCount">{{detailItem.score}}</view>
                        </view>
                        <view class="scoredetail">
                            <uni-rate :readonly="true" :value="detailItem.score/2" :max="5" allow-half :size="size"
                                color="#fff" />
                            <view class="prisedCounts">
                                {{detailItem.prisedCounts}}人点赞
                            </view>
                        </view>
                    </view>
                </view>
            </view>
            <view class="produce">
                <view class="produce_name">剧情介绍:</view>
                <view class="plotDesc">{{detailItem.plotDesc}}</view>
            </view>
            <view class="produce">
                <view class="produce_name">演职人员:</view>
                <scroll-view scroll-x="true" show-scrollbar="true">
                    <view class="scroll-view_H">
                        <view v-for="item in director" :key="item.id" class="scroll_item">
                            <image :src="item.photo" class="photo"></image>
                            <view class="item_name">{{item.name}}</view>
                            <view class="actName">{{item.actName}}</view>
                        </view>
                        <view v-for="item in actors" :key="item.id" class="scroll_item">
                            <image :src="item.photo" class="photo"></image>
                            <view class="item_name">{{item.name}}</view>
                            <view class="actName">饰演:{{item.actName}}</view>
                        </view>
                    </view>
                </scroll-view>
            </view>
            <view class="produce">
                <view class="produce_name">剧照:</view>
                <view class="container">
                    <view v-for="(item,index) in plotPics" :key="index" class="content" :class="{'active':(index-1)%3==0}">
                        <image :src="item" class="img" @click="browseImg(item)"></image>
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
                detailItem: {},
                plotPics: [],
                actors: [],
                director: []
            }
        },
        methods: {
            postData() {
                uni.request({
                    url: `${this.$api}/search/staff/${this.detailItem.id}/1?qq=1844532477`,
                    method: 'POST',
                    data: {},
                    success: res => {
                        this.director = res.data.data
                    },
                });
            },
            postDatas() {
                uni.request({
                    url: `${this.$api}/search/staff/${this.detailItem.id}/2?qq=1844532477`,
                    method: 'POST',
                    data: {},
                    success: res => {
                        this.actors = res.data.data
                    },
                });
            },
            browseImg(item) {
                uni.navigateTo({
                    url: `/pages/imgdetail/imgdetail?url=${item}`
                })
            }
        },
        mounted() {

        },
        onLoad(options) {
            this.detailItem = JSON.parse(options.item)
            this.plotPics = JSON.parse(this.detailItem.plotPics)
            this.postData()
            this.postDatas()
        },
        filters: {

        },
        computed: {
            size() {
                let size = ''
                uni.getSystemInfo({
                    success: (res) => {
                        size = (res.windowWidth / 375) * 14
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
    .myVideo {
        width: 100%;
    }

    .box {
        padding: 0 20rpx;
    }

    .info {
        margin-top: 20rpx;
        display: flex;
    }

    .cover {
        width: 220rpx;
        height: 300rpx;
        margin-right: 20rpx;
    }

    .detail {
        width: 470rpx;
    }

    .name {
        width: 470rpx;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }

    .basicInfo {
        margin-top: 10rpx;
        font-size: 28rpx;
        color: #666;
        width: 470rpx;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }

    .originalName {
        margin-top: 10rpx;
        font-size: 28rpx;
        color: #666;
        width: 470rpx;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }

    .releaseDate {
        margin-top: 10rpx;
        font-size: 28rpx;
        color: #666;
        width: 470rpx;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }

    .rate {
        margin-top: 10rpx;
        height: 92rpx;
        background: #EEEEEE;
        display: flex;
    }

    .score {
        font-size: 30rpx;
        margin-left: 10rpx;
    }

    .scoreCount {
        font-size: 30rpx;
        color: #f4ea2a;
        margin: 10rpx 0 0 60rpx;
    }

    .prisedCounts {
        margin-top: 10rpx;
        font-size: 28rpx;
        color: #666;
    }

    .scoredetail {
        margin-top: 20rpx;
        margin-left: 100rpx;
    }

    .produce {
        padding: 20rpx 0;
        border-top: 1rpx solid #999999;
        margin-top: 30rpx;
    }

    .produce_name {
        font-size: 30rpx;
        color: #666;
    }

    .plotDesc {
        font-size: 30rpx;
        line-height: 60rpx;
        text-indent: 35px
    }
    .scroll-view_H {
        display: flex;
        margin-top: 20rpx;
    }
    .scroll_item {
        width: 155rpx;
        margin-left: 20rpx;
    }
    .scroll_item:first-child {
        margin-left: 0;
    }
    .photo {
        width: 155rpx;
        height: 200rpx;
    }
    .item_name {
        font-size: 30rpx;
        width: 155rpx;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        margin: 6rpx 0;
    }
    .actName {
        font-size: 28rpx;
        display: flex;
        color: #666;
        width: 155rpx;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }
    .container {
        display: flex;
        flex-wrap: wrap;
    }

    .content {
        width: 220rpx;
        margin-top: 20rpx;
    }

    .img {
        width: 220rpx;
        height: 300rpx;
    }

    .active {
        margin-left: 25rpx;
        margin-right: 25rpx;
    }
</style>
