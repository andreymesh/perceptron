<script>
  /* eslint-disable max-len */

  function getPixel (imgData, index) {
    const newIndex = index * 4,
          imageDataArray = imgData

    return imageDataArray[newIndex + 3]
  }

  export default {
    data () {
      return {
        canvas: null,
        canvasContext: null,
        isDrawing: false,
        splitValue: 5,
        learningRate: 0.6,
        weights: [],
        cells: [],
        canvasSize: 100,
        computedResult: 0,
        realResult: null
      }
    },
    mounted () {
      this.getCanvas()
      this.setInitialWeights()
      this.getCells()
    },
    methods: {
      getCanvas () {
        const canvas = this.$refs.canvasArea

        this.canvas = canvas
        this.canvasContext = canvas.getContext('2d')
        this.canvasContext.imageSmoothingEnabled = false
        this.canvasContext.fillStyle = '#22bb22'
      },

      clearCanvas () {
        this.canvasContext.clearRect(0, 0, this.canvas.width, this.canvas.height)
        this.cells = []
      },

      setInitialWeights () {
        for (let index = 0; index < this.canvasSize * this.canvasSize + 1; index++) {
          this.weights.push(Math.random() * (this.learningRate - (-this.learningRate)) + (-this.learningRate))
        }
      },

      getCells () {
        const canvas = this.canvas,
              currentContext = canvas.getContext('2d'),
              imageDataArray = currentContext
                .getImageData(0, 0, canvas.width, canvas.height)
                .data

        this.canvasContext = currentContext

        let transformedImageDataArray = []

        for (let index = 0; index < this.canvasSize * this.canvasSize; index++) {
          transformedImageDataArray.push(getPixel(imageDataArray, index))
        }

        transformedImageDataArray = transformedImageDataArray.map(imageDataElement => {
          if (imageDataElement > 0) {
            return 1
          }
          else {
            return 0
          }
        });

        this.cells = transformedImageDataArray
      },

      getActivationFunction () {
        let sum = 0;

        sum += this.weights[0];

        for (let index = 1; index < this.canvasSize * this.canvasSize + 1; index++) {
          sum += this.cells[index - 1] * this.weights[index]
        }

        return sum >= 0 ? 1 : 0
      },

      learn () {
        this.getCells();
        this.realResult = confirm('Select either OK for 4 or CANCEL for 6') ? 0 : 1; // eslint-disable-line no-alert
        this.evalWeights(this.realResult)
      },

      evalWeights (realResult) {
        const deltaError = realResult - this.computedResult;

        if (deltaError !== 0) {
          this.weights[0] = this.weights[0] + this.learningRate * deltaError;

          for (let index = 1; index < this.canvasSize * this.canvasSize + 1; index++) {
            this.weights[index] = this.weights[index] + this.learningRate * deltaError * this.cells[index - 1]
          }
        }
      },

      recognizeImage () {
        this.getCells();
        this.computedResult = this.getActivationFunction();

        const resultingValue = this.computedResult === 0 ? 4 : 6;

        alert(resultingValue) // eslint-disable-line no-alert
      },

      startDrawing (event) {
        this.isDrawing = true;
        this.canvasContext.beginPath();
        this.canvasContext.moveTo(
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

          this.canvasContext.lineTo(x, y)
          this.canvasContext.stroke()
        }
      }
    }
  }
</script>

<template>
  <div class="g-wrapper">
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
    <div class="g-controls">
      <button class="g-btn" @click="learn">
        <span>Обучить</span>
      </button>
      <button class="g-btn" @click="recognizeImage">
        <span>Распознать</span>
      </button>
    </div>
  </div>
</template>
