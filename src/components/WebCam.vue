<template>
  <video id="video" ref="video" autoplay>
  </video>
</template>

<script>
export default {
  name: "WebCam",
  components: {
  },
  created: function() {
    // Start Web Cam
    navigator.mediaDevices.getUserMedia({video: {facingMode: 'user'}, audio: false})
      .then((stream) => {
      this.$refs.video.srcObject = stream;
      this.$refs.video.width = 227;
      this.$refs.video.height = 227;

      this.$refs.video.addEventListener('playing', ()=> this.videoPlaying = true);
      this.$refs.video.addEventListener('paused', ()=> this.videoPlaying = false);
    })
  }
};
</script>