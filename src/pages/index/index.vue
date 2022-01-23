<template>
  <view class="container">
    <image class="page-bg" :src="config.bg_img_url" mode="scaleToFill"/>
    <div class="content-wrap">
      <view class="title-container">
        <text class="letter">20</text>
        <text class="letter">22</text>
        <text class="letter">虎</text>
        <text class="letter">年</text>
        <text class="letter">●</text>
        <text class="letter">虎</text>
        <text class="letter">虎</text>
        <text class="letter">生</text>
        <text class="letter">威</text>
      </view>
      <div @tap="changeMusicStatus" class="music">
        <image :class="[musicStatus?'music_bg autoRotate':'music_bg stopRotate']"
               :style="['transform: rotate('+musicIconRotate+'deg);transform-origin:center center;']"
               src="../assets/img/cd.png"></image>
        <image :class="[musicStatus?'cd_zhen cd_zhen_back':'cd_zhen cd_zhen_go']"
               src="../assets/img/cd_zhen.png"></image>
      </div>
      <div>
        <nut-tabs v-model="tabIndex" title-scroll="true" title-gutter="5" class="panel">
          <nut-tabpane v-for="item in categoryList" :title="item.category_name">
            <scroll-view :scroll-x="true" style="white-space: nowrap;">
              <image v-for="img in item.list" :src="img.url" mode="scaleToFill" class="img-item" @tap="selectImg(img)"/>
            </scroll-view>
          </nut-tabpane>
        </nut-tabs>
      </div>

      <div class="avatar-container">
        <div class="avatar-view">
          <canvas canvas-id="canvas" class="canvas" hidden="true"/>
          <image class="avatar-img" :src="avatarImage" mode="scaleToFill"/>
          <image class="avatar-frame" :src="frameImage" mode="scaleToFill" v-if="frameImage!=null"/>
        </div>
        <div class="btn-view">
          <nut-button type="default" class="btn-share" @tap="getUserInfo">使用微信头像</nut-button>
          <button type="default" class="btn-share" open-type="share">分享给好友</button>
          <nut-button type="primary" class="btn-save" color="#ff5143" @tap="saveAvatar">保存头像</nut-button>
        </div>
      </div>
    </div>

  </view>
</template>

<script>

import './index.sass'
import './index.scss'
import {reactive, toRefs} from 'vue';
import Taro from '@tarojs/taro'

const app = Taro.getApp();
export default {

  name: 'Index',
  components: {},

  /**
   * 分享
   * @returns {{path: string, imageUrl: string, title: string}}
   */
  onShareAppMessage() {
    return {
      title: this.config.share_title,
      path: "/pages/index/index",
      imageUrl: this.config.share_img_url,
    };
  },
  /**
   * 分享朋友圈
   * */
  onShareTimeline() {
    return {
      title: this.config.share_title,
      path: '/pages/index/index',  // 分享的路径
      imageUrl: this.config.share_img_url,  // 分享的图片链接
    }
  },
  /**
   * 加载
   */
  onLoad() {
    console.log("onLoad")
    const that = this;

    const userInfo = Taro.getStorageSync('userInfo');
    if (userInfo != null && userInfo != "") {
      const url = userInfo.avatarUrl.substring(0, userInfo.avatarUrl.lastIndexOf("/")) + "/0";
      console.log(url)
      this.avatarImage = url;
    }
    Taro.onBackgroundAudioStop(function () {
      console.log("监听音乐停止")
      that.musicHasStop = true;
    })
  },
  onShow (){
    console.log("onSHow")
    Taro.showLoading({
      title: '加载中',
      mask: true,
    })
    this.loadConfig()
    this.loadCategoryAndPicture();
  },
  setup() {

    function tabSwitch(item, index) {
      console.log(item, index);
    }

    const state = reactive({
      avatarImage: require('../assets/img/avatar_empty.svg'),
      frameImage: null,
      cansWidth: 640,
      cansHeight: 640,
      categoryList: [
        {
          id: 1,
          category_name: "虎年最火",
          list: [
            {
              url: "https://file.koolss.com/avatar_applet/hot/1.png"
            },
            {
              url: "https://file.koolss.com/avatar_applet/hot/2.png"
            },
          ]
        }
      ],
      tabIndex: 0,
      musicStatus: true,
      musicIconRotate: 0,
      musicTimer: null,
      backgroundAudioManager: null,
      musicHasStop:null,
      config: {
        bg_img_url: null,
        bg_video_url: null,
        bg_video_title: null,
        share_title: null,
        share_img_url: null,
      }
    });

    const handleClick = (type, msg, cover = false) => {
      state.show = true;
      state.msg2 = msg;
      state.type = type;
      state.cover = cover;
    };

    return {
      tabSwitch,
      ...toRefs(state),
      handleClick,
    }
  },
  methods: {
    selectImg(item) {
      this.frameImage = item.url
    },
    /**
     * 获取用户信息
     */
    getUserInfo() {
      const that = this;
      // 推荐使用wx.getUserProfile获取用户信息，开发者每次通过该接口获取用户个人信息均需用户确认
      // 开发者妥善保管用户快速填写的头像昵称，避免重复弹窗
      console.log("程序入口")
      // 可以通过 Taro.getSetting 先查询一下用户是否授权了 "scope.record" 这个 scope
      Taro.getSetting().then(res => {
        if (res.authSetting['scope.userInfo']) {
          console.log("已授权")
          return true;
        } else {
          console.log("未授权")
          Taro.authorize({
            scope: 'scope.userInfo'
          })
        }
      }).then(() => {
        const userInfo = Taro.getStorageSync('userInfo');
        console.log("获取缓存用户信息:", userInfo);
        if (userInfo == null || userInfo == "") {
          // 推荐使用wx.getUserProfile获取用户信息，开发者每次通过该接口获取用户个人信息均需用户确认
          // 开发者妥善保管用户快速填写的头像昵称，避免重复弹窗
          Taro.getUserProfile({
            desc: '用于完善会员资料', // 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
            success: (res) => {
              console.log("获取用户信息:", res.userInfo);
              Taro.setStorageSync(
                  'userInfo',
                  res.userInfo
              )
              const url = res.userInfo.avatarUrl.substring(0, res.userInfo.avatarUrl.lastIndexOf("/")) + "/0";
              that.avatarImage = url
              that.frameImage = null
            }
          })
        }else {
          const url = userInfo.avatarUrl.substring(0, userInfo.avatarUrl.lastIndexOf("/")) + "/0";
          that.avatarImage = url
          that.frameImage = null
        }
      })
    },
    /**
     * 授权相册权限
     * @param tempFilePath
     */
    authorizeWritePhotosAlbum: function (tempFilePath) {
      const that = this;
      // 可以通过 Taro.getSetting 先查询一下用户是否授权了 "scope.record" 这个 scope
      Taro.getSetting({
        success: function (res) {
          if (!res.authSetting['scope.writePhotosAlbum']) {
            Taro.authorize({
              scope: 'scope.writePhotosAlbum',
              success: function () {
                that.saveImageToPhotosAlbum(tempFilePath)
              }
            })
          } else {
            that.saveImageToPhotosAlbum(tempFilePath)
          }
        }
      })
    },
    /**
     * 保存绘制好的图片到相册
     * @param tempFilePath
     */
    saveImageToPhotosAlbum: function (tempFilePath) {
      // 用户已经同意小程序使用添加到相册
      Taro.saveImageToPhotosAlbum({
        filePath: tempFilePath,
        success: function (res) {
          Taro.hideLoading()
          Taro.showToast({
            title: '保存成功',
            icon: 'success',
            duration: 2000
          })
        },
        fail: function (res) {
          // Taro.showModal({
          //   title: '您需要授权相册权限',
          //   success: function (res) {
          //     if (res.confirm) {
          //       console.log('用户点击确定')
          //     } else if (res.cancel) {
          //       console.log('用户点击取消')
          //     }
          //   }
          // })
        }
      })
    },

    /**
     * 绘制头像
     */
    drawAvatar: async function () {
      const that = this;
      const ctx = Taro.createCanvasContext('canvas');
      if (this.avatarImage != null) {
        if (this.avatarImage.toString().indexOf("http") != -1) {
          await Taro.downloadFile({
            url: this.avatarImage, //仅为示例，并非真实的资源
            success: function (res) {
              // 只要服务器有响应数据，就会把响应内容写入文件并进入 success 回调，业务需要自行判断是否下载到了想要的内容
              if (res.statusCode === 200) {
                console.log("下载文件：", res)
                ctx.drawImage(res.tempFilePath, 0, 0, that.cansWidth, that.cansHeight)
              }
            }
          })
        } else {
          ctx.drawImage(this.avatarImage, 0, 0, that.cansWidth, that.cansHeight)
        }
      }
      ctx.save();
      if (this.frameImage != null) {
        if (this.frameImage.toString().indexOf("http") != -1) {
          await Taro.downloadFile({
            url: this.frameImage, //仅为示例，并非真实的资源
            success: function (res) {
              // 只要服务器有响应数据，就会把响应内容写入文件并进入 success 回调，业务需要自行判断是否下载到了想要的内容
              if (res.statusCode === 200) {
                console.log("下载文件：", res)
                ctx.drawImage(res.tempFilePath, 0, 0, that.cansWidth, that.cansHeight)
              }
            }
          })
        } else {
          ctx.drawImage(this.frameImage, 0, 0, that.cansWidth, that.cansHeight)
        }
      }
      ctx.draw();
      setTimeout(await function () {
        Taro.canvasToTempFilePath({
          x: 0,
          y: 0,
          width: that.cansWidth,
          height: that.cansHeight,
          destWidth: that.cansWidth,
          destHeight: that.cansHeight,
          canvasId: 'canvas',
          success: function (res) {
            console.log("tempFilePath：", res.tempFilePath)
            that.authorizeWritePhotosAlbum(res.tempFilePath)
          }
        })
      }, 500)
    },
    /**
     * 保存头像
     */
    saveAvatar: function () {
      Taro.showLoading({
        title: '保存中...',
        mask: true
      })
      this.drawAvatar();

    },
    /**
     * 初始化背景音乐
     */
    initMusic: function () {
      const that = this;
      if ((that.backgroundAudioManager != null && that.musicHasStop) || that.backgroundAudioManager == null){
        const backgroundAudioManager = Taro.getBackgroundAudioManager()
        that.backgroundAudioManager = backgroundAudioManager;
        backgroundAudioManager.title = that.config.bg_video_title
        // backgroundAudioManager.epname = '此时此刻'
        // backgroundAudioManager.singer = '许巍'
        // backgroundAudioManager.coverImgUrl = 'https://y.gtimg.cn/music/photo_new/T002R300x300M000003rsKF44GyaSk.jpg?max_age=2592000'
        // 设置了 src 之后会自动播放
        backgroundAudioManager.src = that.config.bg_video_url
        backgroundAudioManager.onEnded(()=>{
          that.initMusic();
        })
        that.countTime();
        console.log("初始化背景音乐完成")
        that.musicHasStop = false;
      }else {
        console.log("已初始化音乐")
      }

    },
    countTime: function () {
      const that = this;
      that.musicTimer = setInterval(function () {
        that.musicIconRotate = (that.musicIconRotate + 2) % 360
      }, 50);
    },
    changeMusicStatus: function () {
      if (this.musicStatus) {
        // 暂停
        this.pauseMusic();
      } else {
        // 播放
        this.playMusic();
      }
    },

    /**
     * 播放
     */
    playMusic: function () {
      const that = this;
      this.musicStatus = true;
      setTimeout(function () {
        that.countTime();
        that.backgroundAudioManager.play();
      }, 1000)

    },
    /**
     * 暂停
     */
    pauseMusic: function () {
      this.musicStatus = false;
      this.backgroundAudioManager.pause();
      clearTimeout(this.musicTimer);
    },

    loadConfig: async function () {
      const that = this;
      Taro.request({
        url: 'https://ag.koolss.com/api/config/getConfig',
        method: 'get',
        success: function (res) {
          console.log(res.data)
          if (res.data.status == 200) {
            that.config = res.data.data
            that.initMusic();
          }
        }
      })
    },
    loadCategoryAndPicture: async function () {
      const that = this;
      Taro.request({
        url: 'https://ag.koolss.com/api/category/getCategoryAndPictureList',
        method: 'get',
        success: function (res) {
          console.log(res.data)
          if (res.data.status == 200) {
            that.categoryList = res.data.data
            if (res.data.data.length > 0 && res.data.data[0].list.length > 0) {
              that.frameImage = res.data.data[0].list[0].url;
            }
            Taro.hideLoading()
          }
        }
      })
    }

  }
}
</script>
<style lang="scss" scoped>

</style>