<template>
  <div>
    <video id="video" ref="video" autoplay></video>
    <canvas ref="canvas" class="border canvasbox"></canvas>
  </div>
</template>

<script>
var model = null;
var video = null;
var canvas;
var context;

export default {
  name: "WebCam",
  components: {
  },
  mounted: function() {
    canvas = this.$refs.canvas;
    context = canvas.getContext("2d");
    video = this.$refs.video;
  },
  created: function() {

    const modelParams = {
        flipHorizontal: true,   // flip e.g for video
        maxNumBoxes: 20,        // maximum number of boxes to detect
        iouThreshold: 0.5,      // ioU threshold for non-max suppression
        scoreThreshold: 0.6,    // confidence threshold for predictions.
    }
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
                runDetection()
            } else {
                //updateNote.innerText = "Please enable video"
            }
        });
    });
    
    function runDetection() {
      model.detect(video).then(predictions => {
          console.log("Predictions: ", predictions);
          model.renderPredictions(predictions, canvas, context, video);
          requestAnimationFrame(runDetection);
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