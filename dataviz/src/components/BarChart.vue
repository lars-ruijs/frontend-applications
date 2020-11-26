<template>
    <div id="barchartdiv">
    <h3>Welke steden zien nu al het nut van P+R parkeren?</h3>
    <p><strong>Klik op de staaf</strong> van een stad om te zien hoe het aantal parkeerplaatsen van de P+R locaties <br> binnen die stad is verdeeld.</p>
	</div>
</template>

<script>
console.log("Hallo van BarChart");

import { select, scaleBand, axisBottom, scaleLinear, max, axisLeft } from "d3"

export default {
    name: "BarChart",
    emits: ["cityname"],
    props: {
        width: {
            type: Number,
            required: true
        },
        height: {
            type: Number,
            required: true
        },
        barData: {
            type: Array,
            required: true
        }
    },
    mounted() {
        // Build the Bar chart with the makeBarChart-function when element gets mounted
        this.makeBarChart();
    },
    methods: {
        makeBarChart() {
            // Filter data (send via prop) to only include cities with >2 and <15 P+R locations 
            // Sort data from highest to lowest. So the city with the most P+R locations comes first. 
            const lessData = this.barData.filter(d => d.prLocations > 2 && d.prLocations < 15).sort((b, a) => a.prLocations - b.prLocations);

            // Set the margins and innerWidth/innerHeight
            const margin = {top: 20, right: 30, bottom: 200, left: 60};
            const innerWidth = this.width - margin.left - margin.right;
            const innerHeight = this.height - margin.top - margin.bottom;

            // Append the svg object to the body of the page
            const svg = select("#barchartdiv")
            .append("svg")
                .attr("width", this.width)
                .attr("height", this.height)
                .attr('class', 'barchart')
            .append("g")
                .attr("transform", `translate(${margin.left}, ${innerHeight/5.5})`);

            // X-axis with scaleBand() used for catigorical elements (here: city names)
            const x = scaleBand()
            .domain(lessData.map(d =>  d.city))
            .range([0, innerWidth])
            .padding(0.2);

            // Add label to X-axis 'City's with P+R'
            svg.append("g")
            .call(axisBottom(x))
            .attr("transform", `translate(0, ${innerHeight})`)
            .append('text')
                .attr('class', 'axis-label')
                .attr('y', 100)
                .attr('x', innerWidth/2)
                .text('Steden met P+R');

            // Rotate the city names (tick text)
            svg.selectAll(".tick text")
            .attr("transform", "translate(-10,0)rotate(-45)")
            .style("text-anchor", "end");

            // Y-axis with scaleLinear(). 
            // Domain starts at 0 and ends at the maximum data value of P+R locations. 
            const y = scaleLinear()
            .domain([0, max(lessData, d => d.prLocations)])
            .range([innerHeight, 0]);

            // Add label to Y-axis with the text "Number of P+R locations"
            svg.append("g")
            .call(axisLeft(y))
            .append('text')
                .attr('class', 'axis-label')
                .attr('y', -40)
                .attr('x', -innerHeight/2+15)
                .attr("transform", `rotate(-90)`)
                .text('Aantal P+R locaties →');

            // Create the bars based on the cityData
            // Give it a class with the name of the city (used for generating pie chart later)
            // Add onclick to invoke the storeCity function to generate or update the pie chart
            svg.selectAll("rect")
            .data(lessData)
            .enter()
            .append("rect")
                .attr("x", d => x(d.city))
                .attr("y", d => y(d.prLocations))
                .attr("width", x.bandwidth())
                .attr("height", d => innerHeight - y(d.prLocations))
                .attr("fill", "#1873cc")
                .attr("class", d => d.city)
                .on("mouseover", function() {select(this).style("cursor", "pointer");})
                .on("click", this.storeCity);
        },

       // This function is performed when a bar is pressed. 
       // From the event, the class name is extracted (contains the name of the selected city) 
       // This value gets emitted as a custom event to the parent component. 
        storeCity(id) {
            const city = id.target.className.baseVal;
            this.$emit('cityname', city);
        }
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    h3 {
        font-size: 1.5em;
        margin: 0;
        color: #325F98;
    }
    p {
        margin: 0.1em 0 0 0;
        color: #797979;
    }
</style>