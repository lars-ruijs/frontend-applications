<template>
  <Header />
  <Main />
  <Footer />
</template>

<script>
import Header from './components/Header.vue'
import Main from './components/Main.vue'
import Footer from './components/Footer.vue'

export default {
  name: 'App',
  components: {
    Main,
    Header,
    Footer
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
  padding: 0 15vw 0 15vw;
}

h2 {
  font-size: 2em;

}

a, h2 {
  color: #325F98;
}

p {
  color: #4E4E4E
}

div.tooltip {
  position: absolute;
  text-align: center;
  max-width: 100%;
  max-height: 100%;
  padding: 0.8em;
  font: 1em Avenir;
  background-color: #B034C6;
  border: 0;
  border-radius: 8px;
  pointer-events: none;
  color: white;
}

text {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

g text.axis-label {
  font-size: 1.5em;
  fill: grey;
}

.tick text {
  font-size: 1.3em;
  color: #0f487f;
}

.pie text {
  fill: white;
}

.pie text.legend {
  fill: #797979;
}

path.domain, .tick line {
  stroke: #a5d2ff;
}
</style>
