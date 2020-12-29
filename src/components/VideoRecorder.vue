<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <v-img
          :src="require('../assets/logo.png')"
          class="my-3"
          contain
          height="200"
        />
      </v-col>
    </v-row>
    <v-row v-if="!isRecording" class="text-center justify-center" transition="slide-x-transition">
      This PWA allows for offline recording and saving of video and audio. Future upgrades
      will allow for directly upload to Youtube, cloud storage, filters and more!
      <v-radio-group v-model="radioGroup" label="Camera Facing Mode" row>
        <v-radio
          v-for="n in cameraOptions"
          :key="n.label"
          :label="n.label"
          :value="n.value"
        ></v-radio>
    </v-radio-group>
    <v-switch
      v-model="audioOpt"
      label="Enable Audio"
      row
    ></v-switch>
    <v-switch
      v-model="videoOpt"
      label="Enable Video"
      row
    ></v-switch>
    </v-row>
    <v-row class="text-center justify-center">
      <v-col class="mb-5">
        <v-btn class="ma-2"
          color="green lighten-2"
          v-on:click="startRecord"
          v-if="!isRecording">
          Record
          <v-icon
            dark
            right
          >
            mdi-video
          </v-icon>
        </v-btn>
        <v-btn class="ma-2"
          color="red darken-2"
          v-on:click="stopRecord"
          v-else>
          Stop
        <v-icon
          dark
          right
        >
          mdi-cancel
        </v-icon>
      </v-btn>
      </v-col>
    </v-row>
    <v-row class="text-center">
      <v-col class="mb-4">
        <v-card v-show="videoOpt && isRecording">
          <v-responsive :aspect-ratio="16/9">
            <canvas></canvas>
            <video id="recorder"></video>
          </v-responsive>
        </v-card>
        <div class="mic text-center justify-center" v-if="!videoOpt && isRecording"></div>
      </v-col>
    </v-row>

</v-container>
</template>

<script>
export default {
  name: 'VideoRecorder',
  data() {
    return {
      canvas: {},
      ctx: {},
      video: {},
      stream: {},
      recordedChunks: [],
      isRecording: false,
      mediaRecorder: {},
      radioGroup: "user",
      videoOpt: true,
      audioOpt: true,
      cameraOptions: [{value: 'user', label: 'Front'}, {value: 'environment', label: 'Rear'}]
    }
  },
  methods: {
    startRecord: function () {
      var that = this
      navigator.mediaDevices.getUserMedia({ video: this.videoOpt, audio: this.audioOpt })
      .then(function(stream) {
          that.stream = stream
          if (that.videoOpt) {
            that.video.srcObject = stream
            that.video.play()
          }
          that.mediaRecorder = new MediaRecorder(that.stream)
          that.mediaRecorder.onerror = that.handleError
          that.mediaRecorder.onstop = that.handleStop
          that.mediaRecorder.ondataavailable = that.handleDataAvailable
          that.isRecording = true
          that.mediaRecorder.start()

      })
      .catch(function(err) {
        alert(err)
      })
    },
    stopRecord: function () {
      if (this.videoOpt) {
        this.video.pause()
      }
      this.mediaRecorder.stop()
    },
    handleStop: function () {
      this.isRecording = false
    },
    handleDataAvailable: function (event) {
      if (event.data.size > 0) {
        this.recordedChunks.push(event.data)
        this.download()
      } else {
        alert('There was an error please try again later!')
      }
    },
    handleError: function (err) {
      alert(err)
    },
    download: function () {
      var blob = new Blob(this.recordedChunks, {
        type: "video/webm"
      })
      var url = URL.createObjectURL(blob)
      var a = document.createElement("a")
      document.body.appendChild(a)
      a.style = "display: none"
      a.href = url
      var n = Math.floor(Math.random() * 10000000)
      a.download = n+".webm"
      a.click()
      window.URL.revokeObjectURL(url)
    }
  },
  mounted() {
    var that = this
    this.canvas = document.querySelector('canvas')
    this.ctx = this.canvas.getContext('2d')
    this.video = document.getElementById('recorder')
    this.video.addEventListener('play', () => {
      function step() {
        that.ctx.drawImage(that.video, 0, 0, that.canvas.width, that.canvas.height)
        requestAnimationFrame(step)
      }
      requestAnimationFrame(step)
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #recorder {
    display: none;
  }
  button {
    display: inline-block;
    border-radius: 0.12em;
    font-family: 'Roboto',sans-serif;
    font-weight: 300;
    text-align: center;
    transition: all 0.2s;
    border:0.1em solid #FFFFFF;
    margin:0 0.3em 0.3em 0;
    width: 200px;
  }
  .flex-container {
    display: flex;
    background-color: DodgerBlue;
    flex-direction: column;
    height: 800px;
  }

.flex-container > div {
  background-color: #f1f1f1;
  margin: 10px;
  padding: 20px;
  font-size: 30px;
}
canvas {
  height: 100%;
  width: 100%;
}

.mic {
	background: red;
	border-radius: 50%;
	margin-left: 45%;
	height: 100px;
	width: 100px;

	box-shadow: 0 0 0 0 rgba(0, 0, 0, 1);
	transform: scale(1);
	animation: pulse 2s infinite;
}

@keyframes pulse {
	0% {
		transform: scale(0.95);
		box-shadow: 0 0 0 0 rgba(0, 0, 0, 0.7);
	}

	70% {
		transform: scale(1.2);
		box-shadow: 0 0 0 15px rgba(0, 0, 0, 0);
	}

	100% {
		transform: scale(0.95);
		box-shadow: 0 0 0 0 rgba(0, 0, 0, 0);
	}
}
</style>
