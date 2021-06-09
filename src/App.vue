<template>
  <div class="main">
    <CorssSlide 
      :config="config"
      @horizontal-prev="onHorizontalPrev"
      @horizontal-next="onHorizontalNext"
      @vertical-prev="onVerticalPrev"
      @vertical-next="onVerticalNext"
    />
    <Tabs
      :config="config"
      @on-change="onChangeTab"
    />
  </div>
</template>

<script>
import CorssSlide from './components/cross-slide'
import Tabs from './components/tabs'

export default {
  name: 'App',
  components: {
    CorssSlide,
    Tabs,
  },
  data() {
    return {
      config: [
        { name: 'tab1', selected: true, index: 0, list: [0,1,2,3,4,5,6,7,8,9] },
        { name: 'tab2', selected: false, index: 10, list: ['a','b','c','d','e','f','g','h','i','j','k'] },
        { name: 'tab3', selected: false, index: 0, list: ['一','二','三','四'] },
      ]
    }
  },
  methods: {
    onChangeTab(index){
      this.changeHorizontal(index)
    },
    changeHorizontal(index) {
      this.config.forEach(element => {
        element.selected = false
      })
      this.config[index].selected = true
    },
    changeVertical(hIndex, vIndex) {
      this.config[hIndex].index = vIndex
    },
    onHorizontalPrev(index) {
      if(index > 0) {
        // console.log('horizontal prev', index)
        this.changeHorizontal(--index)
      }
    },
    onHorizontalNext(index) {
      if(index < this.config.length-1) {
        // console.log('horizontal next', index)
        this.changeHorizontal(++index)
      }
    },
    onVerticalPrev({ horizontalIndex, verticalIndex }) {
      if(verticalIndex > 0){
        this.changeVertical(horizontalIndex, --verticalIndex)
      }
    },
    onVerticalNext({ horizontalIndex, verticalIndex }) {
      if(verticalIndex < this.config[horizontalIndex].list.length - 1){
        this.changeVertical(horizontalIndex, ++verticalIndex)
      }
    },
  },
}
</script>

<style lang="less">
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
body{
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color:#333;
}
#app, .main {
  width: 100vw;
  height: 100vh;
}

.main {
  position: relative;
  .tabs {
    position: absolute;
    left: 50%;
    top: 20px;
    transform: translateX(-50%);
  }
}

</style>