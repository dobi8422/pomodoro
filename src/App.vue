<template>
  <div class="container-fluid">
    <MainBlock/>
  </div>
</template>

<script>
import MainBlock from './components/MainBlock.vue'

export default {
  name: 'App',
  components: {
    MainBlock
  }
}
</script>

<style lang="scss">
@import '~bootstrap/scss/bootstrap';
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
