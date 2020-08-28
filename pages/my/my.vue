<template>
    <view>
        <view class="box">
          <view class="nologin" v-if="user === ''">
              <image src="../../static/image/back.png" class="img"></image>
              <view @click="goLogin">登录/注册</view>
          </view>
            <view class="login" v-else>
                <image :src="user.faceImage" class="faceImage"></image>
                <view>
                   <view class="nickname">{{user.nickname}}</view>
                   <view>ID:{{user.id}}</view> 
                </view>
                <image src="../../static/image/set.png" class="set" @click="editInfo"></image>
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
                user: '',
                flag: false
            }
        },
        methods: {
            goLogin() {
                uni.navigateTo({
                    url: '/pages/login/login'
                })
            },
            editInfo() {
                uni.navigateTo({
                    url: '/pages/editInfo/editInfo'
                })
            }
        },
        mounted() {

        },
        onLoad() {
        },
        onShow() {
            let user = uni.getStorageSync('user')
            if (user) {
                this.user = user
            }
        },
        filters: {

        },
        computed: {

        },
        watch: {

        },
        directives: {

        }
    }
</script>

<style scoped lang="scss">
    .box {
        background-image: url(../../static/image/back.png);
        background-size: cover;
        width: 750rpx;
        height: 400rpx;
    }

    .nologin {
        padding-left: 40rpx;
        padding-top: 150rpx;
        display: flex;
        align-items: center;
        color: #FFFFFF;
        font-size: 40rpx;
        font-weight: 600;
    }

    .img {
        width: 150rpx;
        height: 150rpx;
        border-radius: 50%;
        margin-right: 40rpx;
    }

    .login {
        padding-left: 40rpx;
        padding-top: 150rpx;
        display: flex;
        align-items: center;
        color: #FFFFFF;
    }

    .faceImage {
        width: 150rpx;
        height: 150rpx;
        border-radius: 50%;
        margin-right: 40rpx;
    }

    .nickname {
        margin: 0 0 20rpx 0;
    }
    .set {
        width: 50rpx;
        height: 50rpx;
        position: absolute;
        right: 20rpx;
        top: 20rpx;
    }
</style>
