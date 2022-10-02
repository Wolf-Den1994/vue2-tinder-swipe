<template>
  <div id="app">
    <TinderSwipe />
  </div>
</template>

<script>
import TinderSwipe from './TinderSwipe.vue'

export default {
  name: 'App',
  components: {
    TinderSwipe
  }
}
</script>

<style lang="scss">
body {
  max-width: 360px;
  width: 100%;
  overflow: hidden;
}

#app {
  width: 100%;
  height: 80vh;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
