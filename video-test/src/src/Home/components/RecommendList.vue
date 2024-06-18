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
  (e: 'update:value', value: string | number): void
  (e: 'onChange', checked: Option): void
}>()

const onClickHandle = (item: VideoItem) => {
  emit('update:value', item)
  emit('onChange', item)
}
</script>

<template>
  <div class="recommend-list">
    <h4>精彩推荐</h4>
    <ul>
    <li v-for="(item, i) in list" :key="i" @click="onClickHandle(item)">
      {{ item.title }}
    </li>
  </ul>
  </div>
</template>

<style lang="scss" scoped>
.recommend-list {
}
</style>
