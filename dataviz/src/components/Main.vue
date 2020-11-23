<template>
  <ul>
    <li v-for="city in cityData" :key="city.lat">{{ city.prLocations }}</li>
  </ul>
</template>

<script>
import * as RDWData from "../assets/dataCleaning";

console.log("Hallo boven export");

export default {
  name: 'Main',
  data() {
    return {
      specificationData: '',
      cityData: '',
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      const specData = await RDWData.combinedData();
      this.specificationData = specData;

      const prCityData = await RDWData.cityData(specData);
      this.cityData = prCityData;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
