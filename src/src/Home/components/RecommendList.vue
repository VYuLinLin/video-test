<script setup lang="ts">
type VideoItem = {
  id: string
  image: string
  title: string
  video: string
}

defineProps<{
  value: VideoItem | null
  list: VideoItem[]
}>()

// emit
const emit = defineEmits<{
  (e: 'update:value', value: VideoItem): void
  (e: 'onChange', value: VideoItem): void
}>()

const onClickHandle = (item: VideoItem) => {
  emit('update:value', item)
  emit('onChange', item)
}
</script>

<template>
  <div class="recommend-list">
    <h4>精彩推荐</h4>
    <ul class="recommend-list-ul">
    <li class="recommend-list-ul-li" v-for="(item, i) in list" :key="i" @click="onClickHandle(item)">
      <img class="view-image" :src="item.image" :title="item.title"></img>
      <div class="title-wrap">
        <p class="view-title">{{ item.title }}文字信息就解放军发动空间里的恐惧文字信息就解放军发动空间里的恐惧</p>
      </div>
    </li>
  </ul>
  </div>
</template>

<style lang="scss" scoped>
.recommend-list {
  width: 100%;
  height: auto;
  h4 {
    font-size: 16px;
    padding-bottom: 10px;
  }
  &-ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
    grid-gap: 6px;
    width: 100%;
    &-li {
      height: 145px;
      text-align: center;
      border-radius: 5px;
      overflow: hidden;
      background: linear-gradient(to bottom, #000, #1b1b1b);
      .view-image {
        width: 100%;
        height: 90px;
      }
      .title-wrap {
        width: 100%;
        padding:1px 3px;
        .view-title{
          font-size: 12px;
          color: #ccc;
          text-align: left;
          width: 100%;
          overflow: hidden;
          word-wrap: break-word;
          text-overflow:ellipsis;
          display:-webkit-box;
          -webkit-box-orient:vertical;
          -webkit-line-clamp:2;
        }
      }
    }
  }
}
</style>
