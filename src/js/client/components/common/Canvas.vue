<script>
  import Bus from '../../bus'

  function getPixel (imgData, index) {
    let newIndex = index * 4,
        imageDataArray = imgData

    return imageDataArray[newIndex + 3]
  }

  export default {

    data () {
      return {
        canvas: null,
        context: null,
        isDrawing: false,
        canvasSize: 3
      }
    },
    created () {
      Bus.$on('getImageDataRequest', () => {
        const canvas = this.canvas,
              currentContext = canvas.getContext('2d'),
              imageDataArray = currentContext
                .getImageData(0, 0, canvas.width, canvas.height)
                .data

        this.context = currentContext

        let transformedImageDataArray = []

        console.log(imageDataArray)

        for (let index = 0; index < this.canvasSize * this.canvasSize; index++) {
          transformedImageDataArray.push(getPixel(imageDataArray, index))
        }

        transformedImageDataArray = transformedImageDataArray.map(imageDataElement => imageDataElement > 0 ? 1 : 0)

        Bus.$emit('sendImageData', transformedImageDataArray)
      })
    },
    mounted () {
      const canvas = this.$refs.canvasArea

      this.canvas = canvas
      this.context = canvas.getContext('2d')

      this.context.imageSmoothingEnabled = false
      this.context.fillStyle = '#22bb22'

      console.log(this.context)
    },
    methods: {
      startDrawing (event) {
        this.isDrawing = true
        this.context.beginPath()
        this.context.moveTo(
          event.pageX - this.canvas.offsetLeft,
          event.pageY - this.canvas.offsetTop
        )
      },

      stopDrawing () {
        this.isDrawing = false
      },
      draw (event) {
        if (this.isDrawing) {
          const x = event.pageX - this.canvas.offsetLeft,
                y = event.pageY - this.canvas.offsetTop

          this.context.lineTo(x, y)
          this.context.stroke()
        }
      },
      clearCanvas () {
        this.context.clearRect(0, 0, this.canvas.width, this.canvas.height)
      }
    }
  }
</script>

<template>
  <div class="g-canvas__wrapper">
    <canvas :height="canvasSize"
            :width="canvasSize"
            @mousedown="startDrawing"
            @mouseup="stopDrawing"
            @mouseout="stopDrawing"
            @mousemove="draw"
            ref="canvasArea"></canvas>
    <div class="g-canvas__controls">
      <button class="g-btn" @click="clearCanvas">
        <span>Очистить</span>
      </button>
    </div>
  </div>
</template>