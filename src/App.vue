<template>
  <v-app id="app">
    <div class="app-container">
      <h1>Gesture Voice</h1>

      <WebCam
        v-on:ready="loadingModel = false"
        v-on:model-loaded="modelLoaded"
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

       <div>
         <canvas id="last-track" ref="lastTrack"></canvas>
       </div>

    </div>
  </v-app>
</template>

<script>
import WebCam from './components/WebCam.vue'

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
      status: ""
    }
  },
  created: function() {
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
  background-color: red;
  height: 200px;
  width: 200px;
}
</style>