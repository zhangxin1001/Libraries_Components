<template>
  <div class="clipping">
    <div
      class="canvas_mark"
      @touchstart="startfunc"
      @touchmove="movefunc"
    >
      <canvas
        :width="canvasW"
        :height="canvasH"
        ref="canvas"
      ></canvas>
      <div
        class="mark"
        :style="{width:markW + 'px',height:markH + 'px'}"
      ></div>
    </div>
    <div>
      <input
        type="file"
        @change="changeImg"
        ref="imgfile"
        accept="image/*"
        style="display:none"
      />
      <button @click="selectImg">选择图片</button>
      <button @click="clipping">剪裁</button>
      <button @click="scale(1)">放大</button>
      <button @click="scale(-1)">缩小</button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    let winw = document.documentElement.clientWidth,
      markW = winw * 0.4
    return {
      canvasW: winw,
      canvasH: winw,
      markW: markW,
      markH: markW,
      markLeft: (winw - markW) / 2,
      markTop: (winw - markW) / 2,
      imgW: 0,
      imgH: 0,
      imgLeft: 0,
      imgTop: 0,
      endImg: ''
    }
  },
  methods: {
    selectImg() {
      this.$refs.imgfile.click()
    },
    changeImg() {
      let file = this.$refs.imgfile.files[0]
      if (!file) return
      let fileExample = new FileReader()
      fileExample.readAsDataURL(file)
      fileExample.onload = e => {
        this.img = new Image()
        this.img.src = e.target.result
        this.img.onload = () => {
          this.imgW = this.img.width
          this.imgH = this.img.height
          let i = 0
          if (this.imgW > this.canvasW) {
            i = this.imgW / this.canvasW
            this.imgW = this.canvasW
            this.imgH = this.imgH / i
          } else {
            i = this.imgH / this.canvasH
            this.imgH = this.canvasH
            this.imgW = this.imgW / i
          }
          this.imgLeft = (this.canvasW - this.imgW) / 2
          this.imgTop = (this.canvasH - this.imgH) / 2
          this.renderImg()
        }
      }
    },
    clipping() {
      if (!this.img) return
      let imgData = this.ctx.getImageData(
        this.markLeft,
        this.markTop,
        this.markW,
        this.markH
      )
      let canvas2 = document.createElement('canvas'),
        ctx2 = canvas2.getContext('2d')
      canvas2.width = this.markW
      canvas2.height = this.markH
      ctx2.putImageData(imgData, 0, 0, 0, 0, this.markW, this.markH)
      this.endImg = canvas2.toDataURL('image/png')
    },
    scale(e) {
      if (!this.img) return
      let i = this.imgW / this.imgH,
        i1 = 20,
        i2 = i1 / i
      if (e === 1) {
        this.imgW += i1
        this.imgH += i2
      } else {
        this.imgW -= i1
        this.imgH -= i2
      }
      this.renderImg()
    },
    startfunc(e) {
      let point = e.changedTouches[0]
      this.startX = point.clientX
      this.startY = point.clientY
    },
    movefunc(e) {
      if (!this.img) return
      let point = e.changedTouches[0]
      this.moveX = point.clientX - this.startX
      this.moveY = point.clientY - this.startY
      if (Math.abs(this.moveX) > 10 || Math.abs(this.moveY) > 10) {
        this.imgLeft += this.moveX
        this.imgTop += this.moveY
        this.renderImg()
        this.startX = point.clientX
        this.startY = point.clientY
      }
    },
    renderImg() {
      this.ctx = this.$refs.canvas.getContext('2d')
      this.ctx.clearRect(0, 0, this.canvasW, this.canvasH)
      this.ctx.drawImage(
        this.img,
        this.imgLeft,
        this.imgTop,
        this.imgW,
        this.imgH
      )
    }
  }
}
</script>
<style lang = "less" scoped>
.clipping {
  .canvas_mark {
    position: relative;
    .mark {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      border: 1px solid rgba(0, 0, 0, 0.5);
      background: rgba(0, 0, 0, 0.1);
    }
  }
}
</style>

