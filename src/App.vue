<template>
  <div class="contain">
    <div class="search">
      <Search></Search>
    </div>
    <GdMap></GdMap>
  </div>
</template>

<script>
import GdMap from './components/GdMap.vue'
import Search from './components/Search.vue'
export default {
  name: 'App',
  components: {
    GdMap,
    Search
  }
}
</script>

<style>
  html ,body, .contain {
    height: 100%;
  }
  .search {
    position: absolute;
    margin-top: 50px;
    margin-left: 50px;
    z-index: 1;
  }
</style>
