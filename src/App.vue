<template>
  <div id="app">
      <clip-image @saveImage="saveImage" v-if="!isSave"></clip-image>
      <div class="resultBox" @click="isSave = false" v-else>
          <img :src="data" alt="404">
      </div>
  </div>
</template>

<script>
import ClipImage from './components/ClipImage.vue'

export default {
  name: 'app',
  data () {
    return {
      isSave: false,
      data: ''
    }
  },
  components: {
    ClipImage
  },
  methods: {
    saveImage (payload) {
      this.isSave = true
      this.data = payload
    }
  }
}
</script>

<style lang="less">
.resultBox {
    padding-top: 0.2rem;
}
.resultBox img {
    display: block;
    box-sizing: border-box;
    width: 7.1rem;
    height: 7.1rem;
    border: 0.02rem solid #ddd;
}
</style>
