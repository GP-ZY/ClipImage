<template>
  <div class="clipImageBox">
    <div class="canvasBox" @touchstart="startFunc" @touchmove="moveFunc">
      <canvas :width="CW" :height="CH" ref="canvas"></canvas>
      <div
        class="mark"
        v-show="ISMARK"
        :style="{width:MW+'px',height:MH+'px',left:ML+'px',top:MT+'px'}"
      ></div>
    </div>

    <div class="buttonBox">
      <input type="file" accept="image/*" class="file" ref="file" @change="changeFunc"/>
      <button @click="clickFunc">选择图片</button>
      <button @click="scaleFunc(1)">放大</button>
      <button @click="scaleFunc(0)">缩小</button>
      <button @click="saveFunc">保存图片</button>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    let winW = document.documentElement.clientWidth
    let font = parseInt(document.documentElement.style.fontSize)
    let canvasW = winW - 0.4 * font
    let markW = canvasW * 0.7
    return {
      // 画布大小
      CW: canvasW,
      CH: canvasW,
      // mark大小和位置
      MW: markW,
      MH: markW,
      ML: (canvasW - markW) / 2,
      MT: (canvasW - markW) / 2,
      // 图片大小和位置
      IW: 0,
      IH: 0,
      IL: 0,
      IT: 0,
      // 是否显示mark
      ISMARK: false
    }
  },
  methods: {
    clickFunc () {
      // 触发file的选择文件操作
      this.$refs.file.click()
    },
    changeFunc () {
      // 已经选择了图片
      this.ISMARK = true
      let file = this.$refs.file.files[0]
      if (!file) return false
      // 基于filereader进行文件读取
      let fileExample = new FileReader()
      fileExample.readAsDataURL(file)
      fileExample.onload = ev => {
        // 创建新图片
        this.IMAGE = new Image()
        this.IMAGE.src = ev.target.result
        this.IMAGE.onload = _ => {
          this.IW = this.IMAGE.width
          this.IH = this.IMAGE.height
          // 重新按照比例来计算宽高
          let n = 1
          if (this.IW > this.IH) {
            n = this.IW / this.CW
            this.IW = this.CW
            this.IH = this.IH / n
          } else {
            n = this.IH / this.CH
            this.IH = this.CH
            this.IW = this.IW / n
          }
          this.IL = (this.CW - this.IW) / 2
          this.IT = (this.CH - this.IH) / 2
          // 绘制图片
          this.drawImage()
        }
      }
    },
    scaleFunc (flag) {
      if (!this.IMAGE) return false
      let n = this.IW / this.IH
      let n1 = 20
      let n2 = n1 / n
      if (flag) {
        this.IW += n1
        this.IH += n2
      } else {
        this.IW -= n1
        this.IH -= n2
      }
      this.drawImage()
    },
    startFunc (ev) {
      if (!this.IMAGE) return false
      // 按下时获取手指坐标
      let point = ev.changedTouches[0]
      this.startX = point.clientX
      this.startY = point.clientY
    },
    moveFunc (ev) {
      if (!this.IMAGE) return false
      let point = ev.changedTouches[0]
      this.changeX = point.clientX - this.startX
      this.changeY = point.clientY - this.startY
      if (Math.abs(this.changeX) > 10 || Math.abs(this.changeY) > 10) {
        this.IL += this.changeX
        this.IT += this.changeY
        this.drawImage()
        this.startX = point.clientX
        this.startY = point.clientY
      }
    },
    saveFunc () {
      if (!this.IMAGE) return false
      // 获取指定区域的像素
      let imageData = this.CTX.getImageData(this.ML, this.MT, this.MW, this.MH)
      // 创建新的画布
      let canvas2 = document.createElement('canvas')
      let canvas2CTX = canvas2.getContext('2d')
      canvas2.width = this.MW
      canvas2.height = this.MH
      // 把像素信息放置到画布当中
      canvas2CTX.putImageData(imageData, 0, 0, 0, 0, this.MW, this.MH)
      this.$emit('saveImage', canvas2.toDataURL('image/png'))
    },
    drawImage () {
      // 创建2d渲染画布
      this.CTX = this.$refs.canvas.getContext('2d')
      // 清空画布
      this.CTX.clearRect(0, 0, this.CW, this.CH)
      // 绘制图片
      this.CTX.drawImage(this.IMAGE, this.IL, this.IT, this.IW, this.IH)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.clipImageBox {
//   padding: 0.2rem;
  .canvasBox {
    position: relative;
    overflow: hidden;
    canvas {
      display: block;
      box-sizing: border-box;
      margin: 0 auto;
      width: 7.1rem;
      height: 7.1rem;
      border: 0.02rem solid #ddd;
    }
    .mark {
      box-sizing: border-box;
      position: absolute;
      z-index: 20;
      width: 70%;
      height: 70%;
      border: 0.02rem dashed lightcoral;
      background: rgba(0, 0, 0, 0.2);
    }
  }
  .buttonBox {
    margin-top: 0.3rem;
    .file {
      display: none;
    }
    button {
      margin-right: 0.2rem;
      padding: 0.2rem;
      border: none;
      font-size: 0.28rem;
      background: lightblue;
      cursor: pointer;
    }
  }
}
</style>
