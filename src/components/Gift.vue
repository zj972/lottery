<template>
<div class="gift">
  <img class="crown" src="/static/crown.png" width="80px" height="80px"/>
  <div class="box" :style="`border-color: rgb(${color});`">
    <img class="people" :src="`/static/icon/${data.name}.jpg`"/>
    <span>{{data.name}}</span>
    <app-icon class="prize" :name="data.prize.name" :icon="`/static/prize/${data.prize.name}.jpg`"></app-icon>
    <div class="gift-box">
    </div>
  </div>
</div>
</template>

<script>
import Icon from './Icon'

export default {
  name: 'Gift',
  components: {
    'app-icon': Icon
  },
  props: [
    'data'
  ],
  data () {
    return {
      color: '',
      colorList: ['202,21,36', '248,75,77', '252,156,18', '67,138,8', '30,48,88']
    }
  },
  mounted () {
    this.color = this.colorList[parseInt(Math.random() * this.colorList.length, 10)]
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.gift {
  width: 100%;
  height: 100%;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  .crown {
    position: absolute;
    top: -40px;
    right: -10px;
    transform: rotate(45deg);
  }
  .box {
    width: 250px;
    height: 250px;
    border: 4px solid;
    border-radius: 50px;
    overflow: hidden;
    position: relative;
    span {
      position: absolute;
      bottom: 0;
      display: block;
      width: 242px;
      line-height: 50px;
      text-align: center;
      background: rgba(0, 0, 0, 0.4);
      color: bisque;
    }
    .people {
      width: 100%;
      height: 100%;
    }
    .prize {
      position: absolute;
      right: 0;
      bottom: 0;
    }
  }
}
</style>
