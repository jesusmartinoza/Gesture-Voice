<template>
  <div id="app">
    <div class="container">
      <h1>Gesture Voice</h1>

      <WebCam />
    </div>
  </div>
</template>

<script>
import WebCam from './components/WebCam.vue'

export default {
  name: "App",
  components: {
    WebCam
  },
  created: function() {
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
</style>