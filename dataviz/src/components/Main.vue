<template>
<h1>Hoi</h1>
  <ul>
    <li v-for="city in cityData" :key="city.lat">{{ city.city }}</li>
  </ul>
  <BarChart v-if="cityData.length > 0" :barData="cityData" :width="900" :height="420" @cityname="makePie"/>
  
  <PieChart v-if="pieData.length > 0" :pieCityData="pieData" :width="900" :height="420"/>
</template>

<script>
import * as RDWData from "../assets/dataCleaning";
import BarChart from "./BarChart";
import PieChart from "./PieChart";

console.log("Hallo boven export van Main");

export default {
  name: 'Main',
  components: { BarChart, PieChart },
  data() {
    return {
      specificationData: [],
      cityData: [],
      pieData: [],
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
    makePie(id) {
      const citySpecs = this.specificationData.filter(a => a.city == id);
      this.pieData = citySpecs;
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
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
 g text.axis-label {
  font-size: 1.5em;
  fill: grey;
}
</style>