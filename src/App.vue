<template>
  <div id="app">
    <labelsystem></labelsystem>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue';
import Labelsystem from './views/index.vue';

export default {
  name: 'App',
  components: {
    Labelsystem,
  },
};
</script>

<style lang="stylus">
#app
  font-family Avenir, Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
</style>
