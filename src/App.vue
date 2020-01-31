<template>
  <div id="app">
      <clip-image></clip-image>
      <div class="resultBox">
          <img src="" alt="">
      </div>
  </div>
</template>

<script>
import ClipImage from './components/ClipImage.vue'

export default {
  name: 'app',
  components: {
    ClipImage
  }
}
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
