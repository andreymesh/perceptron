<script>
  import Canvas from './common/Canvas.vue'
  import Controls from './common/Controls.vue'

  export default {
    data () {
      return {
        learningRate: null,
        weights: [],
        cells: []
      }
    },
    components: {
      'c-canvas': Canvas,
      'c-controls': Controls
    }
  }
</script>

<template>
  <div class="g-wrapper">
    <c-canvas/>
    <c-controls/>
  </div>
</template>
