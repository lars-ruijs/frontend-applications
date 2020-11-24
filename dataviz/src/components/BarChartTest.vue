<template>
    <p>{{ innerWidth }}</p>
    <svg :width="width" :height="height" class="barchart">
        <g :transform="`translate(${margin.left}, ${innerHeight/2/2})`">

        </g>
    </svg>
    <h1 v-for="city in barData" :key="city.city">{{ city.city }}</h1>
</template>

<script>
console.log("Hallo van BarChart Test");

import * as d3 from "d3"

export default {
    name: "BarChartTest",
    props: {
        barData: {
            type: Array,
            required: true
        }
    },
    data() {
        return {
            width: 900,
            height: 420,
            margin: {top: 10, right: 30, bottom: 170, left: 80},
            innerHeight: 0,
            innerWidth: 0,
        };
    },
    mounted() {
        //console.log(this.barData.map(d =>  d.city));
        this.inner();
    },
    methods: {
        inner() {
            this.innerHeight = this.height - this.margin.top - this.margin.bottom;
            this.innerWidth = this.width - this.margin.left - this.margin.right;
        },
        xScale() {
            d3.scaleBand()
            .domain(this.barData.map(d =>  d.city))
            .range([0, this.innerWidth])
            .padding(0.2);
        },
        yScale() {
            d3.scaleLinear()
            .domain([0, d3.max(this.barData, d => d.prLocations)])
            .range([this.innerHeight, 0]);
        }
    }
};
</script>

<style scoped>
    h1 {
        font-size: 2em;
    }
</style>