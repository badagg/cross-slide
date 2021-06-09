<template>
  <div class="cross-slide">
    <div class="horizontal" :class="{'transition': touchScrollTransition}" :style="`transform: translateX(${horizontalPosition}px);`">
      <Vertical 
        v-for="(tab,index) in config"
        :key="index"
        :info="tab"
        :height="height"
        :offset="offset.y"
        :touchScrollTransition="touchScrollTransition"
      />
    </div>
  </div>
</template>

<script>
import Vertical from './Vertical'

// 触屏设备事件
const touchEvent = {
  start: 'touchstart',
  move: 'touchmove',
  end: 'touchend',
}

// 鼠标设备事件
const mouseEvent = {
  start: 'mousedown',
  move: 'mousemove',
  end: 'mouseup',
}

const isTouchDevice = 'ontouchstart' in window

const events = isTouchDevice ? touchEvent : mouseEvent

export default {
  name: 'CrossSlide',
  components: {
    Vertical
  },
  props: {
    config: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      width: 0,
      height: 0,
      startPoint: null,
      offset: { x: 0, y: 0 },
      direction: null,
      startTime: null,
      touchScrollTransition: true
    }
  },
  computed: {
    currentTab() {
      return this.config.find(item => item.selected)
    },
    horizontalIndex() {
      return this.config.findIndex(item => item.selected)
    },
    horizontalLength() {
      return this.config.length
    },
    horizontalPosition() {
      return -(this.horizontalIndex * this.width + this.offset.x)
    },
    verticalIndex() {
      return this.currentTab.index
    },
    verticalLength() {
      return this.currentTab.list.length
    },
    verticalPosition() {
      return -(this.verticalIndex * this.height + this.offset.y)
    },
  },
  methods: {
    _bindTouchEvent() {
      this.$el.addEventListener(events.start, this._onStart)
      this.$el.addEventListener(events.move, this._onMove)
      this.$el.addEventListener(events.end, this._onEnd)
    },
    _getEventPoint(e) {
      if(isTouchDevice) {
        const point = e.changedTouches[0]
        return {
          pageX: point.pageX,
          pageY: point.pageY
        }
      } else {
        return {
          pageX: e.pageX,
          pageY: e.pageY
        }
      }
    },
    _onStart(e) {
      const point = this._getEventPoint(e)
      this.startPoint = {
        x: point.pageX,
        y: point.pageY,
      }
      this.startTime = +new Date()
      this.direction = null

      this.touchScrollTransition = false
    },
    _onMove(e) {
      if(!this.startPoint) return

      const point = this._getEventPoint(e)
      const delta = {
        x: point.pageX - this.startPoint.x,
        y: point.pageY - this.startPoint.y,
      }

      let isFirst = false
      let isLast = false

      if(this.direction === null){
        this.direction = Math.abs(delta.x) > Math.abs(delta.y)
      }
      
      if(this.direction) {
        isFirst = this.horizontalIndex === 0 && delta.x > 0
        isLast = this.horizontalIndex === this.horizontalLength - 1 && delta.x < 0

        if(isFirst) {
          delta.x = this._translateOffset(delta.x)
        }
        if(isLast) {
          delta.x = -this._translateOffset(Math.abs(delta.x))
        }

        this.offset = { x: -delta.x, y: 0 }
      } else {
        isFirst = this.verticalIndex === 0 && delta.y > 0
        isLast = this.verticalIndex === this.verticalLength - 1 && delta.y < 0

        if(isFirst) {
          delta.y = this._translateOffset(delta.y)
        }
        if(isLast) {
          delta.y = -this._translateOffset(Math.abs(delta.y))
        }

        this.offset = { x: 0, y: -delta.y }
      }

      e.preventDefault()
      e.stopPropagation()
    },
    _onEnd(e) {
      const point = this._getEventPoint(e)
      const delta = {
        x: point.pageX - this.startPoint.x,
        y: point.pageY - this.startPoint.y,
      }
      const interval = +new Date() - this.startTime
      const minDist = 100
      const minInterval = 200
      const minIntervalDist = 20
      const intervalValid = interval < minInterval

      this.touchScrollTransition = true
      this.startPoint = null

      this.offset = { x: 0, y: 0 }
      
      if(this.direction) {
        if(delta.x > minDist || delta.x > minIntervalDist && intervalValid ) {
          // console.log('horizontal prev')
          this.$emit('horizontal-prev', this.horizontalIndex)
        }
        if(delta.x < -minDist || delta.x < -minIntervalDist && intervalValid) {
          // console.log('horizontal next')
          this.$emit('horizontal-next', this.horizontalIndex)
        }
      } else {
        if(delta.y > minDist || delta.y > minIntervalDist && intervalValid) {
          // console.log('vertical prev')
          this.$emit('vertical-prev', {
            horizontalIndex: this.horizontalIndex,
            verticalIndex: this.verticalIndex
          })
        }

        if(delta.y < -minDist || delta.y < -minIntervalDist && intervalValid) {
          // console.log('vertical next')
          this.$emit('vertical-next', {
            horizontalIndex: this.horizontalIndex,
            verticalIndex: this.verticalIndex
          })
        }
      }
    },
    _translateOffset(offset, limit=30) {
      return offset < limit
          ? offset
          : Math.log10((offset - limit) / limit*2 + 1) * limit*2 + limit
    },
  },
  mounted() {
    this.width = this.$el?.offsetWidth
    this.height = this.$el?.offsetHeight

    this._bindTouchEvent()
  }
}
</script>

<style lang="less">
.cross-slide {
  width: 100%;
  height: 100%;
  overflow: hidden;
  .horizontal {
    width: inherit;
    height: inherit;
    display: flex;
    &.transition {
      transition: transform .3s;
    }
  }
}
</style>