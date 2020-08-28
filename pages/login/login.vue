<template>
  <view class="box">
    <view class="imgurl">
      <image src="../../static/image/back.png" class="img"></image>
    </view>
    <view class="ipt">
      账号<input type="text" v-model="username" placeholder="请输入用户名" />
    </view>
    <view class="ipt">
      密码<input type="password" v-model="password" placeholder="请输入密码" />
    </view>
    <button type="primary" class="btn" @click="login">登录/注册</button>
    <!-- #ifdef APP-PLUS -->
    <view class="else">第三方账号登录</view>
    <view class="imgs">
      <image src="../../static/image/qq.png" class="elseimg" @click="loginQQ"></image>
      <image src="../../static/image/wechat.png" class="imgtwo" @click="loginWechat"></image>
      <image src="../../static/image/shin.png" class="elseimg" @click="loginSina"></image>
    </view>
    <!-- #endif -->
    <!-- #ifdef MP-WEIXIN -->
    <view class="else">第三方账号登录</view>
    <view class="imgs">
      <image src="../../static/image/wechat.png" class="imgtwo" @click="loginWechat"></image>
    </view>
    <!-- #endif -->
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
        username: '',
        password: ''
      }
    },
    methods: {
      // H5登录
      login() {
        uni.request({
          url: `${this.$api}/user/registOrLogin?qq=1844532477`,
          method: 'POST',
          data: {
            username: this.username,
            password: this.password
          },
          success: res => {
            console.log(res)
            if (res.statusCode === 200) {
              uni.showToast({
                title: '登录成功',
                duration: 2000,
                icon: 'none'
              })
              uni.setStorageSync('user', res.data.data)
              uni.switchTab({
                url: '/pages/my/my'
              })
            } else {
              uni.showToast({
                title: '登录失败',
                duration: 2000,
                icon: 'none'
              })
              this.username = ''
              this.password = ''
            }
          },
          fail: () => {},
          complete: () => {}
        });
      },
      // QQ登录
      loginQQ() {
        uni.getProvider({
          service: 'oauth',
          success: (res) => {
            console.log(res.provider)
            if (~res.provider.indexOf('qq')) {
              uni.login({
                provider: 'qq',
                success: (loginRes) => {
                  console.log(loginRes.authResult);
                  uni.getUserInfo({
                    provider: 'qq',
                    success: (infoRes) => {
                      let face = infoRes.userInfo.figureurl_qq
                      let nickname = infoRes.userInfo.nickName
                      let openIdOrUid = infoRes.userInfo.openId
                      console.log('用户昵称为：' + infoRes.userInfo.nickName);
                      console.log('用户头像为：' + infoRes.userInfo.figureurl_qq);
                      console.log('用户ID为：' + infoRes.userInfo.openId);
                      uni.request({
                        url: `${this.$api}/appUnionLogin/qq?qq=1844532477`,
                        method: 'POST',
                        data: {
                          face: face,
                          nickname: nickname,
                          openIdOrUid: openIdOrUid
                        },
                        success: res => {
                          console.log(res);
                          uni.setStorageSync('user', res.data.data)
                          uni.switchTab({
                            url: '/pages/my/my'
                          })
                        },
                        fail: () => {},
                        complete: () => {}
                      });
                    }
                  })
                }
              })
            }
          }
        });
      },
      // 微信登录
      loginWechat() {
        uni.getProvider({
          service: 'oauth',
          success: (res) => {
            console.log(res.provider)
            if (~res.provider.indexOf('weixin')) {
              uni.login({
                provider: 'weixin',
                success: (loginRes) => {
                  console.log(loginRes.authResult);
                  uni.getUserInfo({
                    provider: 'weixin',
                    success: (infoRes) => {
                      let face = infoRes.userInfo.avatarUrl
                      let nickname = infoRes.userInfo.nickName
                      let openIdOrUid = infoRes.userInfo.unionId
                      console.log('用户昵称为：' + infoRes.userInfo.nickName);
                      console.log('用户头像为：' + infoRes.userInfo.avatarUrl);
                      console.log('用户ID为：' + infoRes.userInfo.unionId);
                      uni.request({
                        url: `${this.$api}/appUnionLogin/weixin?qq=1844532477`,
                        method: 'POST',
                        data: {
                          face: face,
                          nickname: nickname,
                          openIdOrUid: openIdOrUid
                        },
                        success: res => {
                          console.log(res);
                          uni.setStorageSync('user', res.data.data)
                          uni.switchTab({
                            url: '/pages/my/my'
                          })
                        },
                        fail: () => {},
                        complete: () => {}
                      });
                    }
                  })
                }
              })
            }
          }
        });
      },
      // 微博登录
      loginSina() {
        uni.getProvider({
          service: 'oauth',
          success: (res) => {
            console.log(res.provider)
            if (~res.provider.indexOf('sinaweibo')) {
              uni.login({
                provider: 'sinaweibo',
                success: (loginRes) => {
                  console.log(loginRes.authResult);
                  uni.getUserInfo({
                    provider: 'sinaweibo',
                    success: (infoRes) => {
                      let face = infoRes.userInfo.avatarUrl
                      let nickname = infoRes.userInfo.nickName
                      let openIdOrUid = infoRes.userInfo.openId
                      console.log(infoRes);
                      console.log('用户昵称为：' + infoRes.userInfo.nickName);
                      console.log('用户头像为：' + infoRes.userInfo.avatarUrl);
                      console.log('用户ID为：' + infoRes.userInfo.openId);
                      uni.request({
                        url: `${this.$api}/appUnionLogin/sinaweibo?qq=1844532477`,
                        method: 'POST',
                        data: {
                          face: face,
                          nickname: nickname,
                          openIdOrUid: openIdOrUid
                        },
                        success: res => {
                          console.log(res);
                          uni.setStorageSync('user', res.data.data)
                          uni.switchTab({
                            url: '/pages/my/my'
                          })
                        },
                        fail: () => {},
                        complete: () => {}
                      });
                    }
                  })
                }
              })
            }
          }
        });
      }
    },
    mounted() {

    },
    onLoad() {

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
    padding: 30rpx;
  }

  .imgurl {
    width: 710rpx;
    text-align: center;
    margin-top: 40rpx;
    margin-bottom: 60rpx;
  }

  .img {
    width: 150rpx;
    height: 150rpx;
    border-radius: 50%;
  }

  .ipt {
    display: flex;
    align-items: center;
    padding: 20rpx;
    border-bottom: 1rpx solid #999;
    margin-bottom: 20rpx;
  }

  .ipt input {
    margin-left: 20rpx;
  }

  .btn {
    margin-top: 60rpx;
    width: 600rpx;
  }

  .else {
    color: #CCCCCC;
    font-size: 28rpx;
    width: 100%;
    text-align: center;
    margin: 40rpx 0 20rpx 0;
  }

  .imgs {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .elseimg {
    width: 70rpx;
    height: 70rpx;
  }

  .imgtwo {
    width: 70rpx;
    height: 70rpx;
    margin: 0 30rpx;
  }
</style>
