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
  <ul class="title-list">
    <li v-for="(item, i) in list" :key="i" @click="onClickHandle(item)">
      {{ item.title }}
    </li>
  </ul>
</template>

<style lang="scss" scoped>
.title-list {
  padding-top: 18px;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  li {
    width: 82px;
    height: 33px;
    line-height: 33px;
    text-align: center;
    background-color: #262626;
    border-radius: 2px;
    margin-bottom: 5px;
  }
}
</style>
