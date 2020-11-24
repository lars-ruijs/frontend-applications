<template>
    <h1>Hi I'm a Pie from {{ pieCityData[0].city }}</h1>
    <div id="piechartdiv">
	</div>
     <ul>
    <li v-for="city in pieCityData" :key="city.lat">{{ city.name }}</li>
  </ul>
</template>

<script>
import * as d3 from "d3"

export default {
    name: "PieChart",
    props: {
        pieCityData: {
            type: Array,
            required: true
        },
        width: {
            type: Number,
            required: true
        },
        height: {
            type: Number,
            required: true
        },
    },
    mounted() {
        this.makePieChart();
    },
    updated() {
        this.updatePieChart();
    },
    methods: {
        arcGenerator() {
            const arc = d3.arc()
            .innerRadius(0)
            .outerRadius(150);
            return arc
        },
        makePieChart() {
            const data = this.pieCityData

            const pieChart = d3.select("#piechartdiv")
            .append("svg")
                .attr('class', 'pie')
                .attr("width", this.width/2)
                .attr("height", this.height)
                .append("g")
                .attr("transform", `translate(${this.width/2/2}, ${this.height/2})`);

            // Data inside pie chart must be generated based on the parking capacity
            const pie = d3.pie()
            .value(d => d.capacity);

            // Select all path elements inside the main pieChart svg element. Bind the data to it.
            const paths = pieChart.selectAll("path")
            .data(pie(data));

            // Add new path elements using enter() and append() if more are needed.
            paths
            .enter()
            .append('path')
            .attr('d', this.arcGenerator())
            .attr("fill", (data, i)=>{ 
                return d3.interpolateRainbow(i/10); 
            })
            .attr("stroke", "white")
            .style("stroke-width", "2px");

            // Select all text elements inside the main pieChart svg element. Bind the data to it.
            const texts = pieChart.selectAll("text")
            .data(pie(data));

            // Add new text elements using enter() and append() if more are needed.
            texts
            .enter()
            .append('text')
            .attr('class', 'pieText')
            .attr("transform", d => `translate(${this.arcGenerator().centroid(d)})`)
            .text(d => d.value)
            .style("text-anchor", "middle");

        },

        updatePieChart() {
            const data = this.pieCityData
            const pieChart = d3.select(".pie g")

            // Data inside pie chart must be generated based on the parking capacity
            const pie = d3.pie()
            .value(d => d.capacity);

            // Select all path elements inside the main pieChart svg element. Bind the data to it.
            const paths = pieChart.selectAll("path")
            .data(pie(data));

            // Update existing path data (d attribute). 
            // Use d3.interpolateRainbow to generate a fill color.
            paths
            .attr('d', this.arcGenerator())
            .attr("fill", (data, i)=>{ 
                return d3.interpolateRainbow(i/10); 
                });

            // Add new path elements using enter() and append() if more are needed.
            paths
            .enter()
            .append('path')
            .attr('d', this.arcGenerator())
            .attr("fill", (data, i)=>{ 
                return d3.interpolateRainbow(i/10); 
            })
            .attr("stroke", "white")
            .style("stroke-width", "2px");

            // Remove the path elements that are not needed annymore. 
            paths
            .exit()
            .remove();

            // Select all text elements inside the main pieChart svg element. Bind the data to it.
            const texts = pieChart.selectAll("text")
            .data(pie(data));

            // Update existing text labels with new data. Set text to the current capacity value. 
            texts
            .attr("transform", d => `translate(${this.arcGenerator().centroid(d)})`)
            .text(d => d.value);

            // Add new text elements using enter() and append() if more are needed.
            texts
            .enter()
            .append('text')
            .attr('class', 'pieText')
            .attr("transform", d => `translate(${this.arcGenerator().centroid(d)})`)
            .text(d => d.value)
            .style("text-anchor", "middle");

            // Remove text elements that are not needed annymore.
            texts
            .exit()
            .remove();
        }
    }
}
</script>

<style scoped>

</style>