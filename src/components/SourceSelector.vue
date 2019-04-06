<template>
  <select @change="setVideoSource" name="" id="">
    <option
      v-for="videoinput in videoinputs"
      :value="videoinput.deviceId"
      :key="videoinput.deviceId"
    >
      {{ videoinput.label }}
    </option>
  </select>
</template>

<script>
export default {
  data() {
    return {
      videoinputs: []
    };
  },
  created() {
    navigator.mediaDevices
      .enumerateDevices()
      .then(devices => {
        return devices.filter(device => device.kind === "videoinput");
      })
      .then(videoinputs => {
        this.videoinputs = videoinputs;
      });
  },
  methods: {
    setVideoSource(e) {
      const deviceId = e.target.value;
      navigator.mediaDevices
        .getUserMedia({
          video: {
            deviceId
          }
        })
        .then(stream => {
          this.$emit("gotStream", stream);
        });
    }
  }
};
</script>
