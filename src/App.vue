<template>
  <div id="app">
    <source-selector @gotStream="setVideoSource" />
    <div id="header-container">
      <span>ソース映像</span>
      <span>スナップショット</span>
    </div>
    <div id="video-box">
      <div class="img-container">
        <video ref="videoEl" autoplay :srcObject.prop="stream"></video>
      </div>
      <div class="img-container">
        <snap-shot :video="this.$refs.videoEl" />
      </div>
    </div>
    <!-- <button @click="sendAnalysisRequest">検出</button> -->
    <Result :features="features" />
  </div>
</template>

<script>
import qs from "query-string";
import SnapShot from "./components/SnapShot.vue";
import Result from "./components/Result.vue";
import SourceSelector from "./components/SourceSelector.vue";
import { dataURItoBlob } from "./utils";
const FACE_API =
  "https://japaneast.api.cognitive.microsoft.com/face/v1.0/detect";
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
    if(!this.intervalID){
      this.count = 0;
      this.intervalID = setInterval(()=>{
        if(this.count < 30){
          this.sendAnalysisRequest();
          this.count++;
        }
      }, 4000);
    }
  },
  methods: {
    setVideoSource(stream) {
      this.stream = stream;
    },
    async takeSnapShot() {
      return new Promise(resolve => {
        this.$eventHub.$on("drawImageDone", png => {
          resolve(png);
        });
        this.$eventHub.$emit("drawImage");
      });
    },
    async sendAnalysisRequest() {
      const img = await this.takeSnapShot();
      const payload = dataURItoBlob(img);
      const url = `${FACE_API}?${qs.stringify(params)}`;

      fetch(url, {
        method: "POST",
        mode: "cors",
        headers: {
          "Content-Type": "application/octet-stream",
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
          if(data.length){
            this.features = data[0].faceAttributes;
            document.body.style.backgroundColor = 'red';
          } else {
            this.features = {};
            document.body.style.backgroundColor = 'white';
          }
        })
        .catch(e => {
          console.error(e);
          alert("エラーが発生しました");
        });
    }
  },
  components: {
    SnapShot,
    Result,
    SourceSelector
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
#header-container {
  display: flex;
  justify-content: space-around;
}
video {
  width: 100%;
  height: 100%;
}
#video-box {
  display: flex;
}
.img-container {
  width: 50%;
}
button {
  width: 260px;
  height: 60px;
  cursor: pointer;
  background: lightgreen;
  font-size: 1.6rem;
}
</style>
