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
    maxNumBoxes: 1,        // maximum number of boxes to detect
    iouThreshold: 0.7,      // ioU threshold for non-max suppression
    scoreThreshold: 0.8,    // confidence threshold for predictions.
}

export default {
  name: "WebCam",
  components: {
  },
  props: ['active'],

  // When UI mounted then init variables
  mounted: function() {
    canvas = this.$refs.canvas;
    context = canvas.getContext("2d");
    video = this.$refs.video;

    // Set canvas
    context.fillStyle = "#FAFBFF";
    context.fillRect(0, 0, canvas.width, canvas.height);
  },
  created: function() {
  },

  methods: {
    /**
     * Start hand tracking detection.

     * When a hand is detected then emit the prediction image.
     */
    runDetection() {
      var vm = this;

      model.detect(video).then(predictions => {
        if(predictions.length > 0) {
          //console.log("Predictions: ", predictions);
          vm.$emit('hand-detected', getHandData(predictions[0].bbox));
        }

        model.renderPredictions(predictions, canvas, context, video);
        requestAnimationFrame(this.runDetection);
      });

      /**
       * Get hand area from canvas
       * and then extract pixel data.
       */
      function getHandData(bbox) {
        var canvasImg = document.createElement('canvas');
        var ctxImg = canvasImg.getContext("2d");
        var img = document.createElement("img");

        canvasImg.width = bbox[2];
        canvasImg.height = bbox[3];

        ctxImg.drawImage(
          canvas,
          bbox[0], // x
          bbox[1], // y
          bbox[2], // width
          bbox[3], // height
          0,
          0,
          bbox[2],
          bbox[3]
        );

        img.width = canvasImg.width;
        img.height = canvasImg.height;
        //img.setAttribute("src", canvasImg.toDataURL());

        return ctxImg.getImageData(0, 0, canvasImg.width, canvasImg.height);
      }
    },

    /**
     * Start video stream on canvas
     */
    startVideo() {
      var vm = this;

      handTrack.startVideo(video).then(function (status) {
        console.log("Video started", status);
        if (status) {
          vm.runDetection()
        }
      });
    }
  },

  watch: {
    /**
     * Handle {active} prop.

     * Load model, start/stop video according to the property.
     */
    active() {
      var vm = this;

      if(this.active) {
        console.log("Loading model...")

        if(model == null) {
          // Load the model.
          handTrack.load(modelParams).then(lmodel => {
            console.log("Model loaded");
            // detect objects in the image.
            model = lmodel;
            vm.$emit('model-loaded');
            vm.startVideo();
          });
        } else {
          vm.$emit('model-loaded');
          vm.startVideo();
        }
      } else {
        handTrack.stopVideo(video)
        context.fillRect(0, 0, canvas.width, canvas.height);
      }
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
  height: calc(100% * .75);
  width: 90%;
}
</style>