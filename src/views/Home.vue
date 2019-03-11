<template>
  <div class="home">

    <!-- a video webcam stream -->
    <video style="border: 1px solid black;" ref="inputVideo" id="inputVideo" width="400" height="300" autoplay loop>
    </video>

    <!-- create a canvas for drawing -->
    <canvas style="position: absolute; left: 38.1%;" ref="drawCanvas" id="drawCanvas" width="400" height="300"></canvas>
  </div>
</template>

<script>

export default {
  name: 'home',
  data: function () {
    return {
      ctracker: null
    }
  },

  mounted () {
    console.log('Home mounted')

    navigator.mediaDevices.getUserMedia({ video: true })
      .then(mediaStream => {
        this.$refs.inputVideo.srcObject = mediaStream
        this.$refs.inputVideo.play()
      })
      .catch(error => console.error('getUserMedia() error:', error))

    var videoInput = this.$refs.inputVideo

    this.ctracker = new clm.tracker()
    this.ctracker.init()
    this.ctracker.start(videoInput)

    this.positionLoop()
    this.drawLoop()
  },

  methods: {

    // get positions
    positionLoop: function () {
      requestAnimationFrame(this.positionLoop)
      var positions = this.ctracker.getCurrentPosition()
      if (typeof(positions) !== 'boolean') {
        console.log(positions)
      }
    },

    // draw positions on the canvas
    drawLoop: function () {
      var canvasInput = this.$refs.drawCanvas
      var cc = canvasInput.getContext('2d')

      cc.clearRect(0, 0, canvasInput.width, canvasInput.height)

      requestAnimationFrame(this.drawLoop)
      cc.clearRect(0, 0, canvasInput, canvasInput.height)
      this.ctracker.draw(canvasInput)
    }
  }
}
</script>
