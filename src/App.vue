<template>
  <div id="app">
    <img alt="J Computer Solutions logo" src="./assets/logo.png">
    <VideoRecorder/>
  </div>
</template>

<script>
import VideoRecorder from './components/VideoRecorder.vue'

export default {
  name: 'App',
  components: {
    VideoRecorder
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
