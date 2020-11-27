<template>

  <!-- Legend with information about the symbols on the map -->
  <svg class="legend" width="300" height="120">
    <circle r="20px" cx="20" cy="20" fill="#B034C6" style="opacity: .5"></circle>
    <circle r="5px" cx="20" cy="20" fill="#B034C6" style="stroke: white; stroke-width: 1"></circle>
    <text x="50" y="25" style="fill: #797979">P+R Locatie</text>
    <circle r="5px" cx="170" cy="20" fill="#3474C6" style="stroke: #ABC2E0; stroke-width: 2"></circle>
    <text x="185" y="25" style="fill: #797979">Stadscentrum</text>
    <circle r="10px" cx="20" cy="80" fill="#B034C6" style="opacity: .5"></circle>
    <circle r="15px" cx="50" cy="80" fill="#B034C6" style="opacity: .5"></circle>
    <circle r="25px" cx="95" cy="80" fill="#B034C6" style="opacity: .5"></circle>
    <text x="135" y="85" style="fill: #797979">Parkeercapaciteit</text>
  </svg>

  <div id="mapdiv">
    <svg class="prmap">
        <g transform="translate(300,400), scale(.45)"></g>
    </svg>
  </div>

</template>

<script>
import { select, geoMercator, geoPath, zoom, json  } from "d3"
import { feature } from "topojson"

export default {
  name: 'Map',
  props: {
        combinedData: {
            type: Array,
            required: true
        },
        cityData: {
            type: Array,
            required: true
        }
    },
  mounted() {
    // Execute function makeMap to create the map
    this.makeMap();
  },
  methods: {
      makeMap() {
        // Select the svg element .prmap (inside template)
        const mapChart = select(".prmap")

        // Select the group element inside the svg
        const g = select(".prmap g")
    
        // Create a 'normal' Mercator projection using geoMercator() from D3. 
        // Set the scale and center the default projection. 
        const projection = geoMercator().scale(100000).center([5.116667,52.17000])
        const pathGenerator = geoPath().projection(projection)
        
        // Create a map of the Netherlands using topojson from Cartomap. Fetch using D3.
        // Use topojson feature to convert to geoJSON
        json('https://cartomap.github.io/nl/wgs84/landsdeel_2020.topojson').then(data => {
            const areas = feature(data, data.objects.landsdeel_2020)
            
            // Select al path-elements inside the svg. Bind the data to it and generate the paths used for displaying the map.
            g.selectAll('path')
            .data(areas.features)
            .enter()
            .append('path')
                .attr('d', pathGenerator)
                .attr('fill', '#F2F2F2')

            // Define the zoom options using D3 zoom. Use scaleExtent to set a minimum and maximum allowed scale. 
            // Add zoom event to group element inside svg. 
            const zooming = zoom()
            .scaleExtent([-2, 5])
            .on('zoom',  (e) => {
                g.attr('transform', e.transform)
            });
            
            // Create a div for the tooltips
            const div = select("body").append("div")
            .attr("class", "tooltip")
            .style("opacity", 0);

            // Incoming data from props. combinedData is filtered to not include the city without a name. 
            const cleanData = this.combinedData.filter(d => d.city != null)
            const cityData = this.cityData;

            // Empty array to store LineString objects in
            const link = []

            // Create LineString objects, with source and target coordinates.
            // Used to create the connecting lines between P+R and cities.  
            cleanData.forEach(function(row){
                const city = cityData.filter(d => d.city === row.city)
                const source = [row.lng, row.lat]
                const target = [city[0].lng, city[0].lat]
                const topush = {type: "LineString", coordinates: [source, target]}
                link.push(topush)
            })

            // Select the connection lines inside the svg. Bind the linestring data to it.
            // Append new path elements (lines)
            g.selectAll("connectLines")
            .data(link)
            .enter()
            .append("path")
                .attr("d", function(d){ return pathGenerator(d)})
                .style("fill", "none")
                .style("stroke", "#BABABA")
                .style("stroke-width", 1)

            // Select al the capacity circles. Bind the specification data to it.
            // Apend new circles with a radius of the P+R parking capacity. 
            // Add tooltip data to it.
            g.selectAll("capacityCircles")
            .data(cleanData)
            .enter()
            .append('circle')
                .attr('cx', function(d){
                    const long = d.lng
                    const lat = d.lat
                    return projection([+long, +lat])[0]  
                })
                .attr('cy', function(d){
                    const long = d.lng
                    const lat = d.lat
                    return projection([+long, +lat])[1]
                })
                .attr('r', function(d){
                            const size = d.capacity/20+10;
                            return size;
                })
                .attr('fill', '#B034C6')
                .style("opacity", .5)
                .on("mouseover", function(event,d) {
                    div.transition()
                        .duration(200)
                        .style("opacity", .9);
                    div.html(`<strong>${d.name}</strong><br/> ${d.capacity} parkeerplaatsen`)
                        .style("left", (event.pageX - 135) + "px")
                        .style("top", (event.pageY - 100) + "px");
                })
                .on("mouseout", function() {
                    div.transition()
                        .duration(500)
                        .style("opacity", 0);
                });

            // Select al the facility circles. Bind the specification data to it.
            // Apend new circles with a radius of 5px and a white border. 
            // Add tooltip data to it.
            g.selectAll('facilityCircles')
            .data(cleanData)
            .enter()
            .append('circle')
                .attr('cx', function(d){
                    const long = d.lng
                    const lat = d.lat
                    return projection([+long, +lat])[0]  
                })
                .attr('cy', function(d){
                    const long = d.lng
                    const lat = d.lat
                    return projection([+long, +lat])[1]
                })
                .attr('r', "5px")
                .attr('fill', '#B034C6')
                .style("stroke", "white")
                .style("stroke-width", 1)
                .on("mouseover", function(event,d) {
                    div.transition()
                        .duration(200)
                        .style("opacity", .9);
                    div.html(`<strong>${d.name}</strong><br/> ${d.capacity} parkeerplaatsen`)
                        .style("left", (event.pageX - 135) + "px")
                        .style("top", (event.pageY - 100) + "px");
                })
                .on("mouseout", function() {
                    div.transition()
                        .duration(500)
                        .style("opacity", 0);
                });

            // Select al the city circles. Bind the city data to it.
            // Apend new circles with a radius of 5px and a blue border. 
            g.selectAll('cityCircles')
            .data(cityData)
            .enter()
            .append('circle')
                .attr('cx', function(d){
                    const long = d.lng
                    const lat = d.lat
                    return projection([+long, +lat])[0]  
                })
                .attr('cy', function(d){
                    const long = d.lng
                    const lat = d.lat
                        return projection([+long, +lat])[1]
                })
                .attr('r', '5px')
                .attr('fill', '#3474C6')
                .style("stroke", "#ABC2E0")
                .style("stroke-width", 2)

            // Call the zoom function for the svg. 
            mapChart.call(zooming);
        })

        }
  
      }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    #mapdiv {
        display: flex;
        justify-content: flex-end;
        margin: 2em -15.55vw 6em -15.55vw;
        background-color: #0f477e;
        border: solid #c2c2c2;
        border-width: 1px 0 1px 0;
    }
    svg.prmap {
        width: 100%;
        height: 35em;
    }
    svg.legend {
        margin: 3em 0 1em 0;
    }
</style>

