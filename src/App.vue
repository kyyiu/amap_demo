<template>
  <div class="contain">
    <GdMap></GdMap>
  </div>
</template>

<script>
import GdMap from './components/GdMap.vue'

export default {
  name: 'App',
  components: {
    GdMap
  }
}
</script>

<style>
  html ,body, .contain {
    height: 100%;
  }
</style>
