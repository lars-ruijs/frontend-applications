<template>
    <div id="piechartdiv">
        <h2>Hoe is de parkeercapaciteit verdeeld?</h2>
        <p>Bij P+R locaties in {{ pieCityData[0].city }}.</p>
	</div>
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
            .outerRadius(130);
            return arc
        },
        makePieChart() {
            const data = this.pieCityData

            const pieChart = d3.select("#piechartdiv")
            .append("svg")
                .attr('class', 'pie')
                .attr("width", this.width)
                .attr("height", this.height)
                .append("g")
                .attr("transform", `translate(${this.width/2}, ${this.height/4.5})`);

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

            const dots = pieChart.selectAll(".dots")
            .data(data);

            dots
            .enter()
            .append("circle")
            .attr('class', 'dots')
            .attr("cx", -210)
            .attr("cy", function(d,i){ return 180 + i*25}) // 100 is where the first dot appears. 25 is the distance between dots
            .attr("r", 7)
            .attr("fill", (data, i)=>{ 
                return d3.interpolateRainbow(i/10); 
            })

            const legendText = pieChart.selectAll(".legend")
            .data(data);

            legendText
            .enter()
            .append("text")
                .attr('class', 'legend')
                .attr("x", -190)
                .attr("y", function(d,i){ return 180 + i*25}) // 100 is where the first dot appears. 25 is the distance between dots
                .text(function(d){ return `${d.name} - ${d.capacity}`})
                .attr("text-anchor", "left")
                .style("alignment-baseline", "middle")
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
            const texts = pieChart.selectAll(".pieText")
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

            const dots = pieChart.selectAll(".dots")
            .data(data);

            dots
            .attr("cx", -210)
            .attr("cy", function(d,i){ return 180 + i*25}) // 100 is where the first dot appears. 25 is the distance between dots
            .attr("r", 7)
            .attr("fill", (data, i)=>{ 
                return d3.interpolateRainbow(i/10); 
            })

            dots
            .enter()
            .append("circle")
            .attr('class', 'dots')
            .attr("cx", -210)
            .attr("cy", function(d,i){ return 180 + i*25}) // 100 is where the first dot appears. 25 is the distance between dots
            .attr("r", 7)
            .attr("fill", (data, i)=>{ 
                return d3.interpolateRainbow(i/10); 
            })

            dots
            .exit()
            .remove();

            const legendText = pieChart.selectAll(".legend")
            .data(data);

            legendText
            .attr("y", function(d,i){ return 180 + i*25}) // 100 is where the first dot appears. 25 is the distance between dots
            .text(function(d){ return `${d.name} - ${d.capacity}`})


            legendText
            .enter()
            .append("text")
                .attr('class', 'legend')
                .attr("x", -190)
                .attr("y", function(d,i){ return 180 + i*25}) // 100 is where the first dot appears. 25 is the distance between dots
                .text(function(d){ return `${d.name} - ${d.capacity}`})
                .attr("text-anchor", "left")
                .style("alignment-baseline", "middle")

            legendText
            .exit()
            .remove();
        }
    }
}
</script>

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
        margin-left: 1.2em;
    }
</style>