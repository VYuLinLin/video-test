<script setup lang="ts">
type VideoItem = {
  id: string
  image: string
  title: string
  video: string
}
const props = defineProps<{
  value: VideoItem | null
}>()
</script>

<template>
  <div class="player">
    <div class="video">
      <video v-if="value && value.video" :src="value.video"></video>
    </div>
    <div class="video"></div>
    <div class="time">

    </div>
    <div class="save-btn">保存到桌面 观看更多视频</div>
  </div>
</template>

<style lang="scss" scoped>
.player {
  .times {

  }
  .save-btn {
    width: 282px;
    height: 40px;
    font-size: 16px;
    line-height: 40px;
    text-align: center;
    border-radius: 43px;
    color: #241110;
    background: linear-gradient(#61feee, #01d6be);
    margin: 28px auto 30px;
  }
}
</style>
