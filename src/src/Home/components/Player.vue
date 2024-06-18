<script setup lang="ts">
import { ref, watch, onMounted, onBeforeUnmount } from 'vue'
import Hls from 'hls.js' // 用于播放m3u8格式
import Flv from 'flv.js' // 用于播放hlv格式
import DPlayer from 'dplayer'

type VideoItem = {
  id: string
  image: string
  title: string
  video: string
}

const props = defineProps<{
  value: VideoItem
}>()
//
const timeList = ref([])
const setTimeList = (duration: number) => {
  if (duration <= 0) {
    timeList.value = []
  } else {
    const time = Math.floor(duration / 5)
    timeList.value = [time, time * 2, time * 3, time * 4]
  }
}

// dplayer 实例
let dp = null
let hls = null
let flv = null
// 销毁
const dpDestroy = () => {
  dp && dp.destroy()
  hls && hls.destroy()
  flv && flv.destroy()
}
// 初始化
const loadVideo = () => {
  dpDestroy()
  dp = new DPlayer({
    container: document.getElementById('dplayer'), //播放器容器元素
    autoplay: false, //是否自动播放
    live: false, //是否直播
    theme: '#b7daff', //主题色 底部进度条相关颜色
    loop: false, //是否循环播放
    lang: 'zh-cn', //语言
    screenshot: true, //开启截图，如果开启，视频和视频封面需要允许跨域
    hotkey: true, //开启热键，支持快进、快退、音量控制、播放暂停
    preload: 'auto', //视频预加载，可选值: 'none', 'metadata', 'auto'
    // volume: 0, //初始化音量
    playbackSpeed: [0.5, 1, 2, 4, 8], //播放速度
    mutex: false, //互斥，阻止多个播放器同时播放，当前播放器播放时暂停其他播放器
    preventClickToggle: false, //阻止点击播放器时候自动切换播放/暂停
    // logo: '', //在左上角展示一个 logo，你可以通过 CSS 调整它的大小和位置
    video: {
      pic: props.value.image, // 视频封面
      url: '', //视频链接
      thumbnails: '', //视频缩略图，可以使用 DPlayer-thumbnails生成
      type: 'customHls', //可选值: 'auto', 'hls', 'flv', 'dash', 'webtorrent', 'normal' 或其他自定义类型,
      customType: {
        //自定义播放类型文件《type需要设置为'customHls'》
        customHls: function (video, player) {
          hls?.destroy()
          hls = new Hls()
          hls.loadSource(
            video.src ||
              props.value.video ||
              // 测试地址
              'https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8' ||
              'https://sf1-cdn-tos.huoshanstatic.com/obj/media-fe/xgplayer_doc_video/hls/xgplayer-demo.m3u8'
          )
          hls.attachMedia(video)
        },
        //自定义播放类型文件《type需要设置为'customFlv'》
        customFlv: function (video, player) {
          flv?.destroy()
          flv = Flv.createPlayer({
            type: 'flv',
            hasAudio: false,
            url: video.src || props.value.video
          })
          flv.attachMediaElement(video)
          flv.load()
        }
      }
    }
  })
  // 考虑隐私问题，初始化时把音量设为0,才可自动播放
  // dp.volume(percentage: number, nostorage: boolean, nonotice: boolean): 设置视频音量
  // dp.volume(0, true, true)

  // 视频组件加载完成后自动播放
  dp.on('loadedmetadata', function () {
    // console.log('loadedmetadata 播放总时长：', dp.video.duration, '秒');
    setTimeList(dp.video.duration)
    dp.play()
  })
  // 监听 durationchange 事件
  dp.on('durationchange', () => {
    // console.log('durationchange 播放总时长：', dp.video.duration, '秒')
    // 获取总时长，单位为秒
    setTimeList(dp.video.duration)
  })
  // 视频流出问题时触发
  dp.on('error', function () {
    if (document.getElementById('dplayer')) {
      //当前时间＋1
      let time = Number(Math.round(dp.video.currentTime + 1))
      //重新加载视频
      loadVideo()
      //跳转到更新后的时间
      dp.seek(time)
    }
  })
}

// 播放数据变化后，重新加载播放器
watch(props.value, (newVal, oldVal) => {
  if (newVal.id === oldVal.id) return
  if (!dp) return
  loadVideo()
  // dp.switchVideo({
  //   pic: props.value.image,
  //   url: props.value.video || 'https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8'
  // })
})

onMounted(() => {
  loadVideo()
})

onBeforeUnmount(() => {
  dpDestroy()
})

// 跳转到指定时间
const seekHandle = (item) => {
  if (!dp) return
  dp.seek(item)
}
const formatTime = (time: number) => {
  const i = String(parseInt(String((time / 60) % 60))).padStart(2, '0')
  const s = String(parseInt(String(time % 60))).padStart(2, '0')
  return `${i}:${s}`
}
</script>

<template>
  <div class="player">
    <div id="dplayer"></div>
    <div class="desc">
      {{ value.title }} 文字信息就解放军发动空间里的恐惧文字信息就解放军发动空间里的恐惧
    </div>
    <ul class="times">
      <li v-for="(item, i) in timeList" :key="i" @click="seekHandle(item)">
        {{ formatTime(item) }}
      </li>
    </ul>
    <!-- 时间有限，弹框功能没有实现 -->
    <div class="save-btn">保存到桌面 观看更多视频</div>
  </div>
</template>

<style lang="scss" scoped>
.player {
  margin-top: 8px;
  #dplayer {
    height: 193px;
    border-radius: 8px;
    overflow: hidden;
  }
  .desc {
    font-size: 14px;
    color: #ccc;
    height: 44px;
    line-height: 44px;
    border-bottom: 1px solid #323232;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
  .times {
    display: flex;
    gap: 6px;
    margin-top: 12px;
    li {
      width: 62px;
      height: 30px;
      line-height: 30px;
      text-align: center;
      color: black;
      border-radius: 16px;
      background: linear-gradient(#61feee, #01d6be);
    }
  }
  .save-btn {
    width: 282px;
    height: 40px;
    font-size: 16px;
    font-weight: 600;
    line-height: 40px;
    text-align: center;
    border-radius: 43px;
    color: #241110;
    background: linear-gradient(#61feee, #01d6be);
    margin: 28px auto 30px;
  }
}
</style>
