<template>
  <div class="mask">
  </div>
</template>

<script>
  export default {
    name: 'Mask',
    props: [
    ],
    data () {
      return {
      }
    },
    mounted () {
      const DURATION = 8000
      const LENGTH = 120

      new Confetti({
        width: window.innerWidth,
        height: window.innerHeight,
        length: LENGTH,
        duration: DURATION
      });

      setTimeout(function () {
        new Confetti({
          width: window.innerWidth,
          height: window.innerHeight,
          length: LENGTH,
          duration: DURATION
        });
      }, DURATION / 2);
    },
    methods: {

    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  $radius: 90;
  .mask {
  }
</style>
