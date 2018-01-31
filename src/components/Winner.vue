<template>
<div class="winner" v-if="show">
  <div class="header">
    <el-button type="text" icon="el-icon-close" @click="close"></el-button>
  </div>
  <!-- 结果 -->
  <div class="result-box">
    <div class="result" v-for="(item, index) of data" :key="index">
      <app-gift :data="item"></app-gift>
    </div>
  </div>
</div>
</template>

<script>
import Gift from './Gift'

export default {
  name: 'Winner',
  components: {
    'app-gift': Gift
  },
  props: [
    'show',
    'data'
  ],
  data () {
    return {
    }
  },
  mounted () {
  },
  methods: {
    close () {
      this.$emit('closeMask')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.winner {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  z-index: 9000;
  .header {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    width: 100%;
    padding-right: 40px;
    height: 90px;
    .el-button {
      font-size: 50px;
    }
  }
  .result-box {
    display: flex;
    justify-content: space-around;
    align-items: center;
    flex-wrap: wrap-reverse;
    overflow: hidden;
    width: 80%;
    height: calc(100% - 90px);
    margin: 0 auto;
    ::-webkit-scrollbar {
      display: none;
    }
    .result {
      width: 23%;
    }
  }
}
</style>
