<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="J Computer Solutions LLC Logo"
          class="shrink mr-2"
          contain
          src="./assets/logo.png"
          transition="scale-transition"
          width="40"
        />
      </div>
      Pocket Recorder
      <v-spacer></v-spacer>
      J Computer Solutions LLC
    </v-app-bar>

    <v-main>
      <VideoRecorder/>
    </v-main>
  </v-app>
</template>

<script>
import VideoRecorder from './components/VideoRecorder';

export default {
  name: 'App',

  components: {
    VideoRecorder,
  },

  data: () => ({
    //
  }),
};
</script>
