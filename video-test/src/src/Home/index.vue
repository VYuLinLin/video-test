<script setup lang="ts">
import { ref } from 'vue'
import TitleList from './components/TitleList.vue'
import Player from './components/Player.vue'
import RecommendList from './components/RecommendList.vue'
import data from '@/assets/video/videos.json'

type VideoItem = {
  id: string
  image: string
  title: string
  video: string
}

const { videos } = data
let activeVideoItem = ref<VideoItem>(null)
</script>

<template>
  <div class="home">
    <!-- 标题列表 -->
    <TitleList v-model:value="activeVideoItem" :list="videos" />
    <!-- 视频播放 -->
    <Player :value="activeVideoItem" />
    <!-- 推荐列表 -->
    <RecommendList v-model:value="activeVideoItem" :list="videos" />
  </div>
</template>

<style scoped>
.home {
  width: 100%;
  height: 100%;
  padding: 0 16px;
}
</style>
