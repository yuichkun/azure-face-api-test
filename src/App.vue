<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the "westus" region.
// If you use a free trial subscription key, you shouldn't need to change
// this region.

const uriBase =
  "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";
// Request parameters.
const params = {
  returnFaceId: "true",
  returnFaceLandmarks: "false",
  returnFaceAttributes:
    "age,gender,headPose,smile,facialHair,glasses,emotion," +
    "hair,makeup,occlusion,accessories,blur,exposure,noise"
};

export default {
  name: "app",
  components: {}
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
