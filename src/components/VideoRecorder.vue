<template>
  <div class="video-recorder">
    <canvas></canvas>
    <video id="recorder"></video>
    <br>
    <button v-on:click="startRecord" v-if="!isRecording"> Start Recording 🎥</button>
    <button v-on:click="stopRecord" v-else> Stop Recording ❌ </button>
  </div>
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
      mediaRecorder: {}
    }
  },
  methods: {
    startRecord: function () {
      var that = this
      navigator.mediaDevices.getUserMedia({ video: true, audio: true })
      .then(function(stream) {
          that.stream = stream
          that.video.srcObject = stream
          that.video.play()
          that.mediaRecorder = new MediaRecorder(that.stream)

          that.mediaRecorder.ondataavailable = that.handleDataAvailable
          that.isRecording = true
          that.mediaRecorder.start()

      })
      .catch(function(err) {
        alert(err)
      })
    },
    stopRecord: function () {
      this.video.pause()
      this.mediaRecorder.stop()
      this.isRecording = false
    },
    handleDataAvailable: function (event){
      if (event.data.size > 0) {
        this.recordedChunks.push(event.data)
        this.download()
      } else {
        alert('There was an error please try again later!')
      }
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
      var d = new Date()
      var n = d.toUTCString()
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
}
</style>
