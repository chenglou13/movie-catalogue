<template>
  <div id="app">
    <MovieTest/>
  </div>
</template>

<script>
import MovieTest from './components/MovieTest.vue'

export default {
  name: 'App',
  components: {
    MovieTest
  }
}
</script>

<style>
html {
  height: 100vh;
  width: 100%;
  background-color: #f7f7f7;
}
body {
  margin: 0 20px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
