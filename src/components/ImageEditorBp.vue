<template>
  <div style="display: inline-block;">
      <canvas ref="myCanvas" width="500" height="500"  style="position: absolute; top: 0; left: 0;"></canvas>
  </div>
</template>

<script>
export default {
  name: 'CanvasImage',
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
      originalImage: null,// 存储原始图像
      showOverlay: false,
      mouseX: 0,
      mouseY: 0,
    }
  },
  mounted() {
    this.loadImage();
    this.addMouseEventListeners();
  },
  methods: {
    loadImage() {
      const canvas = this.$refs.myCanvas;
      const ctx = canvas.getContext('2d');
      const img = new Image();
      img.src = this.imagePath; // 替换为你的图片路径
      // img.src = 'D:\\Download\\ComfyUI_00287_.png'; // 替换为你的图片路径
      img.onload = () => {
        ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
        this.originalImage = img; // 存储加载的图像
      };
    },
    resetImage() {
      const canvas = this.$refs.myCanvas;
      const ctx = canvas.getContext('2d');
      if (this.originalImage) {
        ctx.clearRect(0, 0, canvas.width, canvas.height); // 清除整个画布
        ctx.drawImage(this.originalImage, 0, 0, canvas.width, canvas.height); // 重绘原始图像
      }
    },
    addMouseEventListeners() {
      const canvas = this.$refs.myCanvas;
      canvas.addEventListener('mousedown', this.handleMouseDown);
      canvas.addEventListener('mousemove', this.handleMouseMove);
      canvas.addEventListener('mouseup', this.handleMouseUp);
      canvas.addEventListener('mouseleave', this.handleMouseLeave);
    },
    handleMouseDown(event) {
      if (event.button === 0) { // 只处理左键
        this.isDrawing = true;
        this.clearArea(event);
      }
    },
    handleMouseMove(event) {
      this.mouseX = event.offsetX;
      this.mouseY = event.offsetY;
      if (this.isDrawing) {
        this.clearArea(event);
      }
    },
    handleMouseUp() {
      this.isDrawing = false;
    },
    handleMouseLeave() {
      this.showOverlay = false;
    },
    clearArea(event) {
      const canvas = this.$refs.myCanvas;
      const ctx = canvas.getContext('2d');
      const rect = canvas.getBoundingClientRect();
      const x = event.clientX - rect.left;
      const y = event.clientY - rect.top;
      const radius = this.radius;

      ctx.save();
      ctx.globalCompositeOperation = 'destination-out';
      ctx.beginPath();
      ctx.arc(x, y, radius, 0, 2 * Math.PI, false);
      ctx.fill();
      ctx.restore();
    }
  },
  watch: {
    mouseX() {
      this.showOverlay = true;
    },
    mouseY() {
      this.showOverlay = true;
    }
  },
  beforeDestroy() {
    const canvas = this.$refs.myCanvas;
    canvas.removeEventListener('mousedown', this.handleMouseDown);
    canvas.removeEventListener('mousemove', this.handleMouseMove);
    canvas.removeEventListener('mouseup', this.handleMouseUp);
    canvas.removeEventListener('mouseleave', this.handleMouseLeave);
  },
}
</script>

<style scoped>
canvas {
  border: 1px solid black;
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 500px;
  height: 500px;
  background-color: rgba(200, 200, 200, 0.5); /* 浅灰色遮罩层 */
  pointer-events: none; /* 避免遮挡鼠标事件 */
}
</style>
