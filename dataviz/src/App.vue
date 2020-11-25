<template>
  <Main />
</template>

<script>
import Main from './components/Main.vue'

export default {
  name: 'App',
  components: {
    Main
  }
}
</script>

<style>
html {
  margin: 0;
  padding: 0;
  background-color: #fafafa;
}

#app {  
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #325F98;
  margin: 0;
  padding: 0 4vw 0 4vw;
}
</style>
