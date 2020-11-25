<template>
    <div id="piechartdiv">
        <h2>Hoe is de parkeercapaciteit verdeeld?</h2>
        <p>Bij P+R locaties in {{ pieCityData[0].city }}.</p>
        <svg class="pie" :width="this.width" :height="this.height">
            <g :transform="`translate(${this.width/2}, ${this.height/4.5})`"></g>
        </svg>
	</div>
</template>

<script>
import { select, arc, pie, interpolateRainbow } from "d3"

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
        // Build the Pie chart with the buildPieChart-function when element gets mounted
        this.buildPieChart();
    },
    updated() {
        // Update the data inside the pie chart by using the updated() lifecycle of Vue
        // Executes the buildPieChart-function (uses D3 update pattern)
        this.buildPieChart();
    },
    methods: {
        buildPieChart() {
            // Get the data (send via prop)
            const data = this.pieCityData

            // Select the G-element inside the pie SVG
            const pieChart = select(".pie g")

            // Set the inner and outer radius and make a pie circle using d3.arc()
            const arcGenerator = arc()
            .innerRadius(0)
            .outerRadius(130);

            // Data inside pie chart must be generated based on the parking capacity
            const pieGenerator = pie()
            .value(d => d.capacity);

            // Select all path elements inside the main pieChart svg element. Bind the data to it.
            const paths = pieChart.selectAll("path")
            .data(pieGenerator(data));

            // Update existing path data (d attribute). 
            // Use d3.interpolateRainbow to generate a fill color.
            paths
            .attr('d', arcGenerator)
            .attr("fill", (data, i)=>{ 
                return interpolateRainbow(i/10); 
                });

            // Add new path elements using enter() and append() if more are needed.
            paths
            .enter()
            .append('path')
                .attr('d', arcGenerator)
                .attr("fill", (data, i)=>{ 
                    return interpolateRainbow(i/10); 
                })
                .attr("stroke", "white")
                .style("stroke-width", "2px");

            // Remove the path elements that are not needed annymore. 
            paths
            .exit()
            .remove();

            // Select all text elements with class 'pieText' (numbers inside the pie slices) that are inside the main pieChart svg. Bind the data to it.
            const texts = pieChart.selectAll(".pieText")
            .data(pieGenerator(data));

            // Update existing text labels with new data. Set text to the current capacity value. 
            texts
            .attr("transform", d => `translate(${arcGenerator.centroid(d)})`)
            .text(d => d.value);

            // Add new text elements using enter() and append() if more are needed.
            texts
            .enter()
            .append('text')
                .attr('class', 'pieText')
                .attr("transform", d => `translate(${arcGenerator.centroid(d)})`)
                .text(d => d.value)
                .style("text-anchor", "middle");

            // Remove text elements that are not needed annymore.
            texts
            .exit()
            .remove();

            // Select all circles with the class 'dots' (legend dots) that are inside the main pieChart svg. Bind the data to it.
            const dots = pieChart.selectAll(".dots")
            .data(data);

            // Update existing dot circles with new data. Set the correct cy position and set the correct fill color. 
            dots
            .attr("cy", function(d,i){ return 180 + i*25}) // 180 is where the first dot appears. 25 is the distance between the dots
            .attr("fill", (data, i)=>{ 
                return interpolateRainbow(i/10); 
            })

            // Add new dot circles using enter() and append() if more are needed.
            dots
            .enter()
            .append("circle")
                .attr('class', 'dots')
                .attr("cx", -210) // Align the dots to the left
                .attr("cy", function(d,i){ return 180 + i*25}) // 180 is where the first dot appears. 25 is the distance between the dots
                .attr("r", 7)
                .attr("fill", (data, i)=>{ 
                    return interpolateRainbow(i/10); 
                })

            // Remove the dot circles that are not needed annymore.
            dots
            .exit()
            .remove();

            // Select all text elements with the class 'legend' (text for the legend) that are inside the main pieChart svg. Bind the data to it.
            const legendText = pieChart.selectAll(".legend")
            .data(data);

            // Update existing legend text with new data. Set the correct y position and display the correct name of the P+R facility. 
            legendText
            .attr("y", function(d,i){ return 180 + i*25}) // 180 is where the first text appears. 25 is the distance between the text elements
            .text(function(d){ return `${d.name} - ${d.capacity}`})

            // Add new legend text elements using enter() and append() if more are needed.
            legendText
            .enter()
            .append("text")
                .attr('class', 'legend')
                .attr("x", -190)
                .attr("y", function(d,i){ return 180 + i*25}) // 180 is where the first text appears. 25 is the distance between text elements
                .text(function(d){ return `${d.name} - ${d.capacity}`})
                .attr("text-anchor", "left")
                .style("alignment-baseline", "middle")

            // Remove the legend text elements that are not needed annymore.
            legendText
            .exit()
            .remove();
        },
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    h2 {
        font-size: 1.5em;
        margin: 0 0 0 0;
        color: #325F98;
    }
    p {
        margin: 0.1em 0 1.8em 0;
        color: #797979;
    }
    #piechartdiv {
        margin-left: 1.6em;
    }
</style>