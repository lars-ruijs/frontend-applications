<template>
  <h2>Wat is het nut van P+R parkeren?</h2>
  <p>
      Park en Ride locaties zijn speciale parkeervoorzieningen die vaak buiten het centrum van een stad liggen en die direct aansluiting bieden op openbaar vervoer om verder naar een eindbestemming te reizen. P+R locaties liggen daarom regelmatig bij een treinstation waar vandaan reizigers naar alle uithoeken van het land kunnen reizen. Toch is dat lang niet meer het enige gebruiksdoel. P+R locaties zijn ook bij uitstek geschikt om (voordelig) te parkeren tijdens een stadsbezoek.
  </p>
  <p>
      De noodzaak om buiten het stadscentrum te kunnen parkeren groeit. De gemeente Amsterdam heeft bijvoorbeeld sinds januari van dit jaar <a href="https://assets.amsterdam.nl/publish/pages/956709/agenda_autoluw.pdf" target="_blank">een agenda</a> opgesteld om het centrum van de hoofdstad autoluw te maken. Er moet een parkeerluw stadshart komen en parkeervrije stadsstraten met meer ruimte voor fietsers, voetgangers en het OV. Parkeertarieven in het centrum worden volgens het plan verhoogd om zo te ontmoedigen dat mensen in de binnenstad hun auto parkeren. 
  </p>

  <div class="featured">
    <p>Het centrum van Amsterdam moet autoluw worden: meer ruimte voor fietsers, <br> voetgangers en het OV. P+R locaties kunnen daaraan bijdragen.</p>
  </div>

  <p>
      Een belangrijk onderdeel van het plan “Amsterdam Autoluw” is onder andere de maatregel om P+R faciliteiten uit te breiden. Volgens de gemeente worden bestaande P+R locaties van de stad jaarlijks door meer dan 1 miljoen mensen gebruikt. Nu zorgen die locaties nog voor zo’n 2% lagere parkeerdruk in het centrum. Dat moet volgens de gemeente meer worden door onder andere bestaande locaties uit te breiden en nieuwe locaties te openen. Toch is Amsterdam niet de stad met de meeste P+R locaties. Rotterdam staat stipt op één met 17 P+R locaties rondom het stadscentrum. Sinds de zomer van 2017 heeft Rotterdam drie P+R voorzieningen gratis gemaakt voor OV-reizigers om het gebruik ervan te stimuleren. Ook in Rotterdam wordt het aantal parkeerplaatsen in de binnenstad <a href="https://www.mobiliteitsplatform.nl/artikel/parkeerplaatsen-verdwijnen-in-autoluw-rotterdam" target="_blank">verminderd</a>. In 2020 moeten er 3000 straatparkeerplaatsen zijn verwijderd.
  </p>

  <div class="featured">
    <p>Rotterdam staat stipt op één met 17 P+R locaties <br> rondom het stadscentrum.</p>
  </div>

  <p>
      Als je kijkt naar het aantal P+R locaties van steden, valt op dat niet alleen grote steden meerdere P+R locaties hebben. Ook kleinere steden als Voorburg en Helmond hebben alsnog vier P+R locaties rondom hun stadscentrum. Hieronder staat een overzicht van steden na Rotterdam die minimaal 3 P+R locaties hebben. <strong>Voor locaties waarvan de parkeercapaciteit onbekend is, is de capaciteit weergegeven als 1 parkeerplek.</strong>
  </p>
  <div class="barPie">
    <!-- Contains custom event "cityname" to generate pie chart with function "makePie" -->
    <BarChart v-if="cityData.length > 0" :barData="cityData" :width="780" :height="580" @cityname="makePie"/>
  
    <PieChart v-if="pieData.length > 0" :pieCityData="pieData" :width="430" :height="610"/>
  </div>


  <h2>Hoe verdelen steden hun P+R locaties over de stad?</h2>
  <p>
      De kaart hieronder laat zien waar P+R locaties te vinden zijn en hoe ver ze van het centrum van de stad liggen. Wat opvalt is dat in het westen van Nederland meer P+R locaties zijn en dat deze locaties ook vaak een grotere capaciteit hebben. Het oosten van het land is het slechts vertegenwoordigd qua aantal P+R locaties per stad en de capaciteit daarvan. De P+R locaties die er zijn liggen wel vaak dichtbij het centrum van die stad.
  </p>

  <div class="featured">
    <p>
      Het oosten van het land is het slechts vertegenwoordigd qua aantal P+R locaties per stad en de capaciteit daarvan.
    </p>
  </div>

  <p> 
      <strong>De onderstaande kaart is interactief</strong>. Je kunt uitzoomen voor een groter overzicht van de verdeling van P+R locaties in het land. Klik op een P+R locatie om de capaciteit ervan in detail te zien. <strong>Voor locaties waarvan de parkeercapaciteit onbekend is, is de capaciteit weergegeven als 1 parkeerplek.</strong>
  </p>
  <Map v-if="cityData.length > 0 && specificationData.length > 0" :combinedData="specificationData" :cityData="cityData" />

 <h2>Conclusie</h2>
 <p>
    Grote steden als Amsterdam en Rotterdam zijn al bezig met het veranderen van hun parkeerbeleid. Parkeren in de binnenstad wordt hier ontmoedigd door het verwijderen van parkeerplekken en het verhogen van parkeertarieven. Wanneer steden als doel hebben om de auto uit het centrum te weren, kunnen P+R locaties een van de maatregelen zijn die gemeenten kunnen nemen om dat te bereiken. In het westen van het land zijn steden al op weg met meerdere P+R locaties per stad, terwijl in het oosten van het land het aanbod nog kleiner is. 
 </p>
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

    // Check if localStorage contains selected city to display a Pie chart. 
    this.localPieData();
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

      // Store the selected city inside local storage.
      localStorage.pieCity = JSON.stringify(citySpecs);

      // Set pieData inside data() to the generated citySpecs.
      this.pieData = citySpecs;
    },
    localPieData() {
      // If localstorage has an item named pieCity, parse it to an object 
      // Set pieData variable (inside data) to it's value.
      if (localStorage.pieCity) {
        const local = JSON.parse(localStorage.getItem('pieCity'));
        this.pieData = local;
      }
    }
  },
};
</script>

<style>
  .barPie {
    display: flex;
    flex-wrap: wrap;
    background-color: white;
    margin: 5em -15.5vw 7em -15.5vw;
    padding: 5em 4.5vw 0 4.5vw;
    border: solid #c2c2c2;
    border-width: 1px 0 1px 0;
  }

  div.featured {
    border: solid #325F98;
    border-width: 0 0 0 5px;
    padding-left: 2em; 
    margin: 3em 0 3em 0;
  }

  div.featured p {
    font-weight: bold;
    font-style: italic;
    font-size: 1.1em;
    color: #325F98;
  }
</style>
