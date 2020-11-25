<template>
<h1>Hoi</h1>
  <ul>
    <li v-for="city in cityData" :key="city.lat">{{ city.city }}</li>
  </ul>
  <div class="barPie">
    <BarChart v-if="cityData.length > 0" :barData="cityData" :width="780" :height="580" @cityname="makePie"/>
  
    <PieChart v-if="pieData.length > 0" :pieCityData="pieData" :width="450" :height="610"/>
  </div>
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
.barPie {
  display: flex;
  flex-wrap: wrap;
  background-color: white;
  margin: 0 -2.55vw 0 -2.55vw;
  padding: 5em 4.5vw 0 4.5vw;
  border: solid #707070;
  border-width: 1px 0 1px 0;
}

text {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

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

path.domain {
  stroke: #a5d2ff;
}

.tick line {
  stroke: #a5d2ff;
}

</style>
