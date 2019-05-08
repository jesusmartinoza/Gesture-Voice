<template>
  <v-app id="app">
    <div class="app-container">
      <h1>Gesture Voice</h1>

      <WebCam
        v-on:ready="loadingModel = false"
        v-on:model-loaded="modelLoaded"
        v-on:hand-detected="drawHand"
        :active="webCamActivated"
      />

      <p class="status">
        <b>{{status}}</b>
      </p>

      <v-btn
       :loading="loadingModel"
       :disabled="loadingModel"
       color="primary"
       depressed
       @click="toggleVideo">
        {{buttonText}}
       </v-btn>

       <h1>{{predictedSentence}}</h1>

       <div>
         <canvas id="last-track" ref="lastTrack"></canvas>
       </div>

    </div>
  </v-app>
</template>

<script>
import WebCam from './components/WebCam.vue';

var model = null;
var letters = [
  "A", "B", "C", "D", "E", "F",
  "G", "H", "I", "J", "K", "L",
  "M", "N", "O", "P", "Q", "R",
  "S", "T", "U", "V", "W", "X",
  "Y", "Z"]

export default {
  name: "App",
  components: {
    WebCam
  },
  data () {
    return {
      loader: null,
      loadingModel: false,
      webCamActivated: false,
      buttonText: "Iniciar traducción",
      status: "",
      predictedSentence: "",
      predicting: false
    }
  },
  created: function() {
  },
  mounted: async function() {
    model = await tf.loadLayersModel('/model.json');
  },
  methods: {
    /**
     * Toggle {webCamActivated} and handle result
     */
    toggleVideo() {
      if(this.webCamActivated) {
        this.status = "Traducción pausada";
        this.buttonText = "Iniciar traducción";
      } else {
        this.loadingModel = true;
        this.status = "Cargando modelo...";
      }
      this.webCamActivated = !this.webCamActivated;
    },

    /**
     * Triggered when WebCam component is loaded
     */
    modelLoaded() {
      this.loadingModel = false;
      this.webCamActivated = true;
      this.status = "Traducción en progreso";
      this.buttonText = "Pausar traducción";
    },

    /**
     * Get image data from WebCam module.
     * Pass image data to TF and predict word/character.
     */
    drawHand(handData) {
      var lastTrack = this.$refs.lastTrack;
      var ctx = lastTrack.getContext("2d");
      var vm = this;

      lastTrack.height = handData.height;
      lastTrack.width = handData.width;

      // Resize image to 28x28
      var scaledImageData = scaleImageData(ctx, handData, 28, 28);
      ctx.putImageData(scaledImageData, 0, 0);

      predict(scaledImageData);

      /**
       * Sum RGB pixels and divided in 3 to get the average color
       */
      function toGrayPixels(imageData) {
        var pixels = [];

        for (var i = 0, n = imageData.data.length; i < n; i += 4) {
          var gray = imageData.data[i] + imageData.data[i + 1] + imageData.data[i + 2];
          pixels.push(gray / 3);
        }

        return pixels;
      }

      /**
       * Use tensorflow to predict which letter is in image
       */
      function predict(imageData) {
        if(!vm.predicting) {
          vm.predicting = true;
          var data = toGrayPixels(imageData).map(x => x / 255.0);
          var tensor = tf.tensor2d(data, [1, 784]).reshape([1, 784, 1])

          const prediction = model.predict(tensor);
          var max = tf.max(prediction).dataSync()[0]

          if(max > 0.85)
            console.log(`Letter: ${letters[tf.argMax(prediction, 1).dataSync()[0]]}, Score: ${max}`);

          vm.predicting = false;
        }
      }

      /**
       * Scale image data to desired size;
       * Normally used to generate a constant image size(Width x Height)
       */
      function scaleImageData(ctx, imageData, desiredWidth, desiredHeight) {
          var newCanvas = document.createElement('canvas');
          var scaleWidth = 1 / (imageData.width / desiredWidth);
          var scaleHeight =  1 / (imageData.height / desiredHeight);

          newCanvas.width = imageData.width;
          newCanvas.height = imageData.height;

          newCanvas.getContext("2d").putImageData(imageData, 0, 0);
          ctx.scale(scaleWidth, scaleHeight);
          ctx.drawImage(newCanvas, 0, 0)

          return ctx.getImageData(0, 0, desiredWidth, desiredHeight);
      }
    }
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Montserrat:400,700');

 /* Set vuetify background color */
.theme--light.application { background: #DADFF2 !important; }

html, .body {
  background-color: #DADFF2;
  font-family: 'Montserrat', sans-serif;
}

h1 {
  font-weight: bold;
  margin-bottom: 1em;
}

.app-container {
  background-color: white;
  border-radius: 1.5em;
  box-shadow: 0px 0px 16px #BEC1D6;
  min-height: 90vh;
  margin: 2em auto;
  padding: 1em;
  text-align: center;
  width: 85%;
}

.status {
  margin-top: 1em;
}

#last-track {
  max-height: 200px;
  max-width: 200px;
}
</style>