<template>
  <div class="vertical" :class="{'transition': info.selected && touchScrollTransition}" :style="`transform: translateY(${position}px);`">
    <div class="fill" :style="`height: ${fillHeight}px`"></div>
    <div class="list-items" v-for="item in list" :key="item" v-text="item"></div>
  </div>
</template>

<script>
export default {
  props: {
    info: {
      type: Object,
      default: () => {}
    },
    height: {
      type: Number,
      default: 0
    },
    offset: {
      type: Number,
      default: 0
    },
    touchScrollTransition: {
      type: Boolean,
      default: false
    },
    preload: {
      type: Number,
      default: 1
    }
  },
  computed: {
    index() {
      return this.info.index
    },
    position() {
      return -(this.index * this.height + (this.info.selected ? this.offset : 0))
    },
    total() {
      return this.info.list.length
    },
    list() {
      let arr = [this.info.list[this.index]]
      let beforeArr = []
      let afterArr = []
      for(let i = 1; i <= this.preload; i++) {
        const beforeItem = this.info.list[this.index - i]
        const afterItem = this.info.list[this.index + i]

        if(beforeItem !== undefined){
          beforeArr.unshift(beforeItem)
        }
        
        if(afterItem !== undefined) {
          afterArr.push(afterItem)
        }
      }

      return [...beforeArr, ...arr, ...afterArr]
    },
    fillHeight() {
      return Math.max(this.index - this.preload, 0) * this.height
    },
  }
}
</script>

<style lang="less">
.vertical {
  width: 100%;
  height: 100%;
  flex-shrink: 0;
  &.transition {
    transition: transform .3s;
  }
  .list-items {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #eee;
  }
}
</style>