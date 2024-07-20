<template>
  <div ref="container" class="canvas-image-container">
    <canvas ref="backgroundCanvas" style="position: relative"></canvas>
    <canvas ref="overlayCanvas"
            @mousedown="handleMouseDown"
            @mousemove="handleMouseMove"
            @mouseup="handleMouseUp"
            @mouseleave="handleMouseLeave"
            @wheel="handleWheel"
    ></canvas>
  </div>
</template>

<script>
export default {
  name: 'ImageEditor',
  props: {
    imagePath: {
      type: String,
      required: true
    },
    radius: {
      type: Number,
      default: 10
    }
  },
  data() {
    return {
      isDrawing: false,
      isErasing: false,
      originalImage: null,// 存储原始图像
      radiusInUse: this.radius,
    };
  },
  mounted() {
    this.loadImage();
  },
  methods: {
    loadImage() {
      const img = new Image();
      img.src = this.imagePath; // 使用传入的图片路径
      img.onload = () => {
        this.originalImage = img; // 存储加载的图像
        this.adjustCanvasSize(img.width, img.height);
        const bgCtx = this.$refs.backgroundCanvas.getContext('2d');
        bgCtx.drawImage(img, 0, 0, img.width, img.height);
      };
    },
    adjustCanvasSize(width, height) {
      this.$refs.backgroundCanvas.width = width;
      this.$refs.backgroundCanvas.height = height;
      this.$refs.overlayCanvas.width = width;
      this.$refs.overlayCanvas.height = height;
    },
    resetImage() {
      const canvas = this.$refs.backgroundCanvas;
      const ctx = canvas.getContext('2d');
      if (this.originalImage) {
        ctx.clearRect(0, 0, canvas.width, canvas.height); // 清除整个画布
        ctx.drawImage(this.originalImage, 0, 0, canvas.width, canvas.height); // 重绘原始图像
      }
    },
    handleMouseDown(event) {
      if (event.button === 0) { // 只处理左键
        this.isDrawing = true;
        this.isErasing = event.altKey;
        this.clearArea(event);
      }
    },
    handleMouseMove(event) {
      if (this.isDrawing) {
        this.clearArea(event);
      }
      this.drawOverlay(event);
    },
    handleMouseUp() {
      this.isDrawing = false;
      this.isErasing = false;
    },
    handleMouseLeave() {
      this.isDrawing = false;
      this.isErasing = false;
      this.removeOverlay();
    },
    handleWheel(event) {
      event.preventDefault();
      if (event.wheelDelta > 0 && this.radiusInUse < 50) {
        this.radiusInUse++;
      }
      if (event.wheelDelta < 0 && this.radiusInUse > 10) {
        this.radiusInUse--;
      }
      this.drawOverlay(event);
    },
    drawOverlay(event) {
      const overlayCanvas = this.$refs.overlayCanvas;
      const ctx = overlayCanvas.getContext('2d');
      const rect = overlayCanvas.getBoundingClientRect();
      const mouseX = event.clientX - rect.left;
      const mouseY = event.clientY - rect.top;
      const radiusInUse = this.radiusInUse;

      ctx.clearRect(0, 0, overlayCanvas.width, overlayCanvas.height); // 清除覆盖层内容

      // 绘制跟随鼠标移动的圆
      ctx.fillStyle = 'rgba(230, 230, 230, 0.8)';
      ctx.beginPath();
      ctx.arc(event.offsetX, event.offsetY, radiusInUse, 0, 2 * Math.PI);
      ctx.fill();
    },
    removeOverlay() {
      const overlayCanvas = this.$refs.overlayCanvas;
      const ctx = overlayCanvas.getContext('2d');
      ctx.clearRect(0, 0, overlayCanvas.width, overlayCanvas.height); // 清除覆盖层内容
    },
    clearArea(event) {
      const canvas = this.$refs.backgroundCanvas;
      const ctx = canvas.getContext('2d');
      const rect = canvas.getBoundingClientRect();
      const x = event.clientX - rect.left;
      const y = event.clientY - rect.top;
      const radiusInUse = this.radiusInUse;

      ctx.save();
      ctx.globalCompositeOperation = this.isErasing ? 'source-over' : 'destination-out'; // 根据模式设置 composite operation
      if (this.isErasing && this.originalImage) {
        // 恢复模式下重绘原始图像的相应区域
        ctx.beginPath();
        ctx.arc(x, y, radiusInUse, 0, 2 * Math.PI, false);
        ctx.clip();
        ctx.drawImage(this.originalImage, 0, 0, canvas.width, canvas.height);
      } else {
        // 抹除模式下抹除区域
        ctx.beginPath();
        ctx.arc(x, y, radiusInUse, 0, 2 * Math.PI, false);
        ctx.fill();
      }
      ctx.restore();
    },
    exportImage() {
      const backgroundCanvas = this.$refs.backgroundCanvas;

      // 创建一个新的 canvas 用于合并背景和覆盖层
      const exportCanvas = document.createElement('canvas');
      exportCanvas.width = backgroundCanvas.width;
      exportCanvas.height = backgroundCanvas.height;
      const exportCtx = exportCanvas.getContext('2d');

      // 绘制背景图像
      if (this.originalImage) {
        exportCtx.drawImage(this.$refs.backgroundCanvas, 0, 0);
      }

      // 导出图片
      const dataURL = exportCanvas.toDataURL('image/png');
      const link = document.createElement('a');
      link.href = dataURL;
      link.download = 'canvas_image.png';
      link.click();
    }
  }
}
</script>

<style scoped>
.canvas-image-container {
  display: inline-block;
  position: relative;
}

canvas {
  display: block;
  position: absolute;
  top: 0;
  left: 0;
}
</style>
