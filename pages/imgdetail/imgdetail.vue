<template>
  <view class="box">
    <image :src="imgUrl" class="img" @click="downlond"></image>
    <lee-popup ref="popup" type="bottom">
      <button type="warn" class="btn" @click="seva">保存图片到本地</button>
      <button type="default" class="bton" @click="clear">取消</button>
    </lee-popup>
  </view>
</template>

<script>
  import LeePopup from '../../components/lee-popup/lee-popup.vue'
  export default {
    name: "",
    components: {
      LeePopup
    },
    props: {},
    data() {
      return {
        imgUrl: '',
        flag: false
      }
    },
    methods: {
      downlond() {
        this.$refs.popup.open()
      },
      seva() {
        let platform = uni.getSystemInfoSync().platform
        if (platform != 'ios' || platform != 'android') {
          uni.showToast({
            title: '暂不支持该功能！',
            duration: 2000,
            icon: 'none'
          });
        } else {
          uni.downloadFile({
            url: this.imgUrl,
            success: res => {
              if (res.statusCode === 200) {
                let tempFilePaths = res.tempFilePath
                uni.saveImageToPhotosAlbum({
                  filePath: tempFilePaths,
                  success() {
                    uni.showToast({
                      title: '下载成功！',
                      duration: 2000
                    });
                  }
                })
              }
            }
          });
        }
      },
      clear() {
        this.$refs.popup.close()
      }
    },
    mounted() {

    },
    onLoad(options) {
      this.imgUrl = options.url
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
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: rgba(0, 0, 0, .9);
  }

  .img {
    width: 90%;
    height: 80%;
    position: absolute;
  }

  .btn {
    margin-bottom: 10rpx;
  }
</style>
