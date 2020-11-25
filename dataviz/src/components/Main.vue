<template>
<h1>Hoi</h1>
  <ul>
    <li v-for="city in cityData" :key="city.lat">{{ city.city }}</li>
  </ul>
  <div class="barPie">
    <!-- Contains custom event "cityname" to generate pie chart with function "makePie" -->
    <BarChart v-if="cityData.length > 0" :barData="cityData" :width="780" :height="580" @cityname="makePie"/>
  
    <PieChart v-if="pieData.length > 0" :pieCityData="pieData" :width="450" :height="610"/>
  </div>
  <Map v-if="cityData.length > 0 && specificationData.length > 0" :combinedData="specificationData" :cityData="cityData" :width="1100" :height="600" />
 
</template>

<script>
import * as RDWData from "../assets/dataCleaning";
import BarChart from "./BarChart";
import PieChart from "./PieChart";
import Map from "./Map";

console.log("Hallo boven export van Main");

export default {
  name: 'Main',
  components: { BarChart, PieChart, Map },
  data() {
    return {
      specificationData: [],
      cityData: [],
      pieData: [],
    };
  },
  mounted() {
    // Execute function fetchData to get the RDW data
    this.fetchData();
  },
  methods: {
    async fetchData() {
      // Fetch the P+R specification data and set value to data()
      const specData = await RDWData.combinedData();
      this.specificationData = specData;

      // Fetch the city data and set value to data()
      const prCityData = await RDWData.cityData(specData);
      this.cityData = prCityData;
    },
    makePie(id) {
      // Get correct data for pie chart based on clicked bar from bar chart. 
      const citySpecs = this.specificationData.filter(a => a.city == id);

      // Set pieData inside data() to the generated citySpecs.
      this.pieData = citySpecs;
    }
  },
};
</script>

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

</style>
