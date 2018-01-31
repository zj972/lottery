<template>
<div class="icon">
  <img v-if="check === 'check'" class="gift" src="/static/gift.png" :width="radius/2 + 'px'" :height="radius/2 + 'px'">
  <img v-if="check === 'none'" class="none" src="/static/none.png" :width="radius + 'px'" :height="radius + 'px'">
  <div :class="check + ' icon-box'">
    <span>{{name}}</span>
    <img class="photo" :src="icon" :width="radius + 'px'" :height="radius + 'px'">
  </div>
</div>
</template>

<script>
export default {
  name: 'Home',
  props: [
    'name',
    'icon',
    'check'
  ],
  data () {
    return {
      radius: 90
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
$radius: 90;
.icon {
  position: relative;
  display: inline-block;
  width: #{$radius}px;
  height: #{$radius}px;
  .gift {
    position: absolute;
    z-index: 202;
    top: -10px;
    right: -10px;
    animation:gift-move 1s 3.3s infinite alternate ease-in-out;
    -webkit-animation:gift-move 1s 3.3s infinite alternate ease-in-out;
  }
  .none {
    position: absolute;
    z-index: 201;
  }
  .icon-box {
    position: relative;
    width: #{$radius}px;
    height: #{$radius}px;
    border-radius: #{$radius}px;
    /*border: 4px solid #ccbb11;*/
    border: 4px solid #B9000A;
    box-shadow: 4px 4px 4px 2px rgb(94, 17, 17);
    overflow: hidden;
    z-index: 100;
    &.check {
      border: 4px solid #7d040b;
    }
    &.none {
      border: 4px solid #afafaf;
    }
    &.focus {
      border: 4px solid #ccbb11;
    }
    .photo {
      position: absolute;
      top: -4px;
      left: -4px;
      z-index: 101;
    }
    span {
      position: absolute;
      bottom: 0;
      display: block;
      width: 100%;
      line-height: 30px;
      text-align: center;
      z-index: 102;
      background: rgba(0, 0, 0, 0.4);
      color: bisque;
    }
  }
  @keyframes gift-move
  {
    0% {
      -webkit-transform: rotate(0deg);
      transform: rotate(0deg);
    }
    33% {
      -webkit-transform: rotate(-5deg);
      transform: rotate(-5deg);
    }
    66% {
      -webkit-transform: rotate(5deg);
      transform: rotate(5deg);
    }
    100% {
      -webkit-transform: rotate(0deg);
      transform: rotate(0deg);
    }
  }
  @-webkit-keyframes gift-move /* Safari and Chrome */
  {
    0% {
      -webkit-transform: rotate(0deg);
      transform: rotate(0deg);
    }
    33% {
      -webkit-transform: rotate(-5deg);
      transform: rotate(-5deg);
    }
    66% {
      -webkit-transform: rotate(5deg);
      transform: rotate(5deg);
    }
    100% {
      -webkit-transform: rotate(0deg);
      transform: rotate(0deg);
    }
  }
}
</style>
