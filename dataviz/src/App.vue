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
  margin: 0;
  padding: 0 9vw 0 9vw;
}

h1 {
  color: #325F98;
}

p {
  color: #4E4E4E
}
</style>
