<template>
  <div>
    <video id="video" ref="video" autoplay></video>
    <canvas ref="canvas"></canvas>
  </div>
</template>

<script>
var model = null;
var video = null;
var canvas;
var context;

const modelParams = {
    flipHorizontal: true,   // flip for video
    maxNumBoxes: 20,        // maximum number of boxes to detect
    iouThreshold: 0.5,      // ioU threshold for non-max suppression
    scoreThreshold: 0.7,    // confidence threshold for predictions.
}

export default {
  name: "WebCam",
  components: {
  },
  props: ['active'],
  mounted: function() {
    canvas = this.$refs.canvas;
    context = canvas.getContext("2d");
    video = this.$refs.video;
  },
  created: function() {
  },

  methods: {
    runDetection() {
      model.detect(video).then(predictions => {
          //console.log("Predictions: ", predictions);
          model.renderPredictions(predictions, canvas, context, video);
          requestAnimationFrame(runDetection);
      });
    }
  },

  watch: {
    active() {
      console.log("Loading model...")

      // Load the model.
      handTrack.load(modelParams).then(lmodel => {
          // detect objects in the image.
          model = lmodel
          console.log("Model loaded")

          handTrack.startVideo(video).then(function (status) {
              console.log("video started", status);
              if (status) {
                  //updateNote.innerText = "Video started. Now tracking"
                  this.runDetection()
              } else {
                  //updateNote.innerText = "Please enable video"
              }
          });
      });
    }
  }
};
</script>

<style scoped>
video {
  display: none;
}

canvas {
  border-radius: 16px;
  height: 300px;
  width: 400px;
}
</style>