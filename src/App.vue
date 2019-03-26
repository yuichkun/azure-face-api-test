<template>
  <div id="app">
    <div id="video-box">
      <video ref="videoEl" autoplay :srcObject.prop="stream"></video>
      <snap-shot :video="this.$refs.videoEl" />
    </div>
    <div v-if="features" id="feature-description">
      <span>Detected Features:</span>
      <ul v-for="(value, key) in features" :key="key">
        <li>
          <span>{{ key }}:</span>
          <div>{{ value }}</div>
        </li>
      </ul>
    </div>
    <button @click="sendAnalysisRequest">Analyze</button>
  </div>
</template>

<script>
import qs from "query-string";
import SnapShot from "./components/SnapShot.vue";
const FACE_API =
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
  data: function() {
    return {
      features: null,
      stream: null
    };
  },
  mounted: function() {
    navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
      this.stream = stream;
    });
  },
  methods: {
    sendAnalysisRequest() {
      const url = `${FACE_API}?${qs.stringify(params)}`;
      const payload = JSON.stringify({
        url: this.$refs.inputImg.src
      });

      fetch(url, {
        method: "POST",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
          "Ocp-Apim-Subscription-Key": process.env.VUE_APP_FACE_API_KEY,
          "Cache-Control": "no-cache"
        },
        body: payload
      })
        .then(data => {
          if (data.ok) {
            return data.json();
          } else {
            return Promise.reject(data);
          }
        })
        .then(data => {
          this.features = data[0].faceAttributes;
        });
    }
  },
  components: {
    SnapShot
  }
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
#video-box {
  display: flex;
}
ul {
  list-style: none;
}
</style>
