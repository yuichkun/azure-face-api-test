<template>
  <canvas></canvas>
</template>

<script>
export default {
  props: ["video"],
  mounted: function() {
    this.ctx = this.$el.getContext("2d");
  },
  methods: {
    drawImage() {
      const canvas = this.$el;
      this.ctx.drawImage(this.video, 0, 0, canvas.width, canvas.height);
      requestAnimationFrame(this.drawImage);
    }
  },
  watch: {
    video: function() {
      this.drawImage();
    }
  }
};
</script>

<style>
canvas {
  width: 50vw;
  height: 100%;
  background: blue;
}
</style>
