<template>
  <v-app id="app">
    <div class="container">
      <h1>Gesture Voice</h1>

      <WebCam
        v-on:ready="loadingModel = false"
        v-on:model-loaded="modelLoaded"
        :active="webCamActive"
      />

      <p class="status">
        <b>{{status}}</b>
      </p>

      <v-btn
         :loading="loadingModel"
         :disabled="loadingModel"
         color="primary"
         depressed
         @click="toggleVideo"
       >
        {{buttonText}}
       </v-btn>

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
      webCamActive: false,
      buttonText: "Iniciar traducción",
      status: ""
    }
  },
  created: function() {
  },
  methods: {
    toggleVideo() {
      if(this.webCamActive) {
        this.webCamActive = false;
        this.status = "Traducción pausada";
      } else {
        this.loadingModel = true;
        this.webCamActive = true;
        this.status = "Cargando modelo..."
      }
    },

    modelLoaded() {
      this.loadingModel = false;
      this.webCamActive = true;
      this.status = "Traducción en progreso";
      this.buttonText = "Pausar traducción";
    }
  },
  watch: {
    /*toggleVideo () {
      // const l = this.loader
      // this[l] = !this[l]
      //
      // setTimeout(() => (this[l] = false), 3000)
      //
      // this.loader = null

    }*/
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Montserrat:400,700');

html, .body {
  background-color: #DADFF2;
  font-family: 'Montserrat', sans-serif;
}

h1 {
  font-weight: bold;
  margin-bottom: 1em;
}

.container {
  background-color: white;
  border-radius: 1.5em;
  box-shadow: 0px 0px 16px #BEC1D6;
  height: 90vh;
  margin: 1em auto;
  padding: 1em;
  text-align: center;
  width: 85%;
}

.status {
  margin-top: 1em;
}
</style>