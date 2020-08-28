<template>
  <view>
    <view class="info">
      <text>头像</text>
      <view class="right">
        <image :src="faceImage" class="faceImage"></image>
        <image src="../../static/image/right.png" class="img" @click="image"></image>
      </view>
    </view>
    <uni-popup ref="popupimg" type="bottom">
      <view class="box">
        <button type="default" @click="changeImage">更换头像</button>
        <button type="default" class="mid" @click="previewImage">查看头像</button>
        <button type="default" @click="backimg">取消</button>
      </view>
    </uni-popup>
    <view class="info">
      <text>昵称</text>
      <view class="right">
        <view class="detail">{{nickname}}</view>
        <image src="../../static/image/right.png" class="img" @click="name"></image>
      </view>
    </view>
    <uni-popup ref="popup" type="bottom">
      <view class="box">
        <view class="confirm">
          <text @click="close">取消</text>
          <text @click="confirm">确定</text>
        </view>
        <view class="lol">
          <input type="text" v-model="changeName" class="changeName" />
        </view>
      </view>
    </uni-popup>
    <picker mode="date" :value="birthday" :start="startDate" :end="endDate" @change="bindDateChange">
      <view>
        <view class="info">
          <text>生日</text>
          <view class="right">
            <view class="detail">{{birthday}}</view>
            <image src="../../static/image/right.png" class="img"></image>
          </view>
        </view>
      </view>
    </picker>
    <picker @change="bindPickerChange" :value="index" :range="list">
      <view>
        <view class="info">
          <text>性别</text>
          <view class="right">
            <view class="detail">{{gender}}</view>
            <image src="../../static/image/right.png" class="img"></image>
          </view>
        </view>

      </view>
    </picker>
    <view class="button">
      <button type="default" @click="clear">清理缓存</button>
      <button type="default" class="mid" @click="editInfo">确认修改</button>
      <button type="default" @click="back">退出登录</button>
    </view>
  </view>
</template>

<script>
  import uniPopup from '../../components/uni-popup/uni-popup.vue'
  export default {
    name: "",
    components: {
      uniPopup,
    },
    props: {},
    data() {
      return {
        faceImage: '',
        nickname: '',
        birthday: '',
        gender: '',
        sex: '',
        changeName: '',
        list: ['女', '男'],
        index: 0,
        Usertoken: '',
        userId: ''
      }
    },
    methods: {
      close() {
        this.$refs.popup.close()
      },
      confirm() {
        this.$refs.popup.close()
        this.nickname = this.changeName
      },
      name() {
        this.$refs.popup.open()
        this.changeName = this.nickname
      },
      bindPickerChange(e) {
        this.sex = e.detail.value
        this.gender = this.list[e.detail.value]
      },
      getDate(type) {
        let date = new Date();
        let year = date.getFullYear();
        let month = date.getMonth() + 1;
        let day = date.getDate();
        if (type === 'start') {
          year = year - 60;
        } else if (type === 'end') {
          year = year;
        }
        month = month > 9 ? month : '0' + month;;
        day = day > 9 ? day : '0' + day;
        return `${year}-${month}-${day}`;
      },
      bindDateChange(e) {
        this.birthday = e.target.value
      },
      clear() {
        let user = uni.getStorageSync('user')
        this.faceImage = user.faceImage
        this.nickname = user.nickname
        this.birthday = user.birthday
        if (user.sex == 1) {
          this.gender = '男'
        } else if (user.sex == 0) {
          this.gender = '女'
        } else {
          this.gender = '请选择'
        }
      },
      image() {
        this.$refs.popupimg.open()
      },
      backimg() {
        this.$refs.popupimg.close()
      },
      previewImage() {
        uni.previewImage({
          urls: [this.faceImage],
          longPressActions: {
            itemList: ['发送给朋友', '保存图片', '收藏'],
            success: (data) => {},
            fail: (err) => {}
          }
        });
      },
      changeImage() {
        uni.chooseImage({
          count: 1,
          success: (res) => {
            let imgUrl = res.tempFilePaths[0]
            uni.uploadFile({
              url: `${this.$api}/user/uploadFace?qq=1844532477&userId=${this.userId}`,
              filePath: imgUrl,
              name: 'file',
              formData: {
                'user': 'test'
              },
              header: {
                headerUserId: this.userId,
                headerUsertoken: this.Usertoken
              },
              success: (uploadFileRes) => {
                console.log(uploadFileRes.data);
                this.faceImage = imgUrl
                this.$refs.popupimg.close()
              }
            });
          }
        });
      },
      editInfo() {
        uni.request({
          url: `${this.$api}/user/modifyUserinfo?qq=1844532477`,
          method: 'POST',
          data: {
            birthday: this.birthday,
            nickname: this.nickname,
            sex: String(this.sex),
            userId: this.userId
          },
          header: {
            headerUserId: this.userId,
            headerUsertoken: this.Usertoken
          },
          success: res => {
            console.log(res)
            if (res.statusCode === 200) {
              uni.showToast({
                title: '修改成功',
                duration: 2000,
                icon: "none"
              })
              uni.setStorageSync('user', res.data.data)
              uni.switchTab({
                url: '/pages/my/my'
              })
            } else {
              uni.showToast({
                title: '修改失败',
                duration: 2000,
                icon: "none"
              })
            }
          },
          fail: () => {},
          complete: () => {}
        });
      },
      back() {
        uni.request({
          url: `${this.$api}/user/logout?qq=1844532477&userId=${this.userId}`,
          method: 'POST',
          data: {},
          success: res => {
            if (res.statusCode === 200) {
              uni.showToast({
                title: '退出成功',
                duration: 2000,
                icon: "none"
              })
              uni.removeStorageSync('user')
              uni.switchTab({
                url: '/pages/my/my'
              })
              let user = uni.getStorageSync('user')
            }
          },
          fail: () => {},
          complete: () => {}
        });
      }
    },
    mounted() {

    },
    onLoad() {
      let user = uni.getStorageSync('user')
      this.faceImage = user.faceImage
      this.nickname = user.nickname
      this.birthday = user.birthday
      this.userId = user.id
      this.Usertoken = user.userUniqueToken
      if (user.sex == 1) {
        this.gender = '男'
      } else if (user.sex == 0) {
        this.gender = '女'
      } else {
        this.gender = '请选择'
      }
      this.index = this.list.findIndex(item => {
        item == this.gender
      })
    },
    filters: {

    },
    computed: {
      startDate() {
        return this.getDate('start');
      },
      endDate() {
        return this.getDate('end');
      }
    },
    watch: {

    },
    directives: {

    }
  }
</script>

<style scoped lang="scss">
  .img {
    width: 35rpx;
    height: 35rpx;
    margin-left: 20rpx;
  }

  .info {
    padding: 20rpx;
    width: 710rpx;
    height: 80rpx;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .faceImage {
    width: 80rpx;
    height: 80rpx;
  }

  .right {
    display: flex;
    align-items: center;
  }

  .detail {
    font-size: 28rpx;
  }

  .box {
    width: 750rpx;
    background: #FFFFFF;
  }

  .confirm {
    padding: 20rpx;
    width: 710rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: #007AFF;
    font-size: 30rpx;
  }

  .lol {
    padding: 40rpx;
  }

  .changeName {
    padding: 10rpx 0;
    width: 100%;
    text-align: center;
    border-bottom: 1rpx solid #666666;
  }

  .button {
    width: 750rpx;
    position: absolute;
    left: 0;
    bottom: 0;
  }

  .mid {
    margin: 10rpx 0;
  }
</style>
