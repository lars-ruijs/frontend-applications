<template>
  <h1>Main.vue</h1>
  <img alt="Vue logo" src="./assets/logo.png">
  <Main />
  <h1>Hello</h1>
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
  background-color: #66a0eb;
}

#app {  
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0;
  padding: 0 2vw 0 2vw;
}
</style>
