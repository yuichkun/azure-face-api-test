<template>
  <canvas></canvas>
</template>

<script>
export default {
  props: ["video"],
  created() {
    this.$eventHub.$on("drawImage", () => {
      this.drawImage();
    });
  },
  mounted: function() {
    this.ctx = this.$el.getContext("2d");
  },
  methods: {
    drawImage() {
      const canvas = this.$el;
      this.ctx.drawImage(this.video, 0, 0, canvas.width, canvas.height);
      const png = canvas.toDataURL("image/png");
      this.$eventHub.$emit("drawImageDone", png);
    }
  }
};
</script>

<style>
canvas {
  width: 100%;
  height: 100%;
}
</style>
