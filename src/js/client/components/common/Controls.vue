<script>
  import Bus from '../../bus'

  export default {
    data () {
      return {}
    },
    mounted () {

    },
    methods: {
      getImageData () {
        Bus.$emit('getImageDataRequest')
      }
    }
  }
</script>

<template>
  <div class="g-controls">
    <button class="g-btn" @click="getImageData">
      <span>Обучить</span>
    </button>
    <button class="g-btn">
      <span>Распознать</span>
    </button>
  </div>
</template>