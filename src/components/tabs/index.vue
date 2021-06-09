<template>
  <div class="tabs">
    <div 
      class="item"
      :class="{'on': item.selected}"
      v-for="(item, index) in config" 
      :key="item.name" 
      v-text="item.name"
      @click="$emit('on-change', index)"
    ></div>
  </div>
</template>

<script>
export default {
  props: {
    config: {
      type: Array,
      default: () => []
    }
  },
}
</script>

<style lang="less">
.tabs {
  display: flex;
  .item {
    margin: 0 10px;
    transition: all .25s;
    &.on{
      color: #fb7299;
    }
  }
}
</style>