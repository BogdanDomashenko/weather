<template>
  <Weather />
</template>

<script>
import Weather from './components/Weather.vue'

export default {
  name: 'App',
  components: {
    Weather
  },
  data: function () {
    return {
      abc: 'ka'
    }
  },
}
</script>

<style>
  body {
    font-family: 'Nunito', sans-serif;
  }
</style>
