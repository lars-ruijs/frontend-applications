<template>
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
        width: {
            type: Number,
            required: true
        },
        height: {
            type: Number,
            required: true
        },
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
    this.makeMap();
  },
  methods: {
      makeMap() {
        const mapChart = select(".prmap")

        const g = select(".prmap g")
        
        const projection = geoMercator().scale(100000).center([5.116667,52.17000])
        const pathGenerator = geoPath().projection(projection)
        
        json('https://cartomap.github.io/nl/wgs84/landsdeel_2020.topojson').then(data => {
            const gemeentes = feature(data, data.objects.landsdeel_2020)
            g.selectAll('path')
            .data(gemeentes.features)
            .enter()
            .append('path')
                .attr('d', pathGenerator)
                .attr('fill', '#F2F2F2')

            const zooming = zoom()
            .scaleExtent([-2, 5])
            .on('zoom',  (e) => {
                g.attr('transform', e.transform)
            });

            const div = select("body").append("div")
            .attr("class", "tooltip")
            .style("opacity", 0);

            const cleanData = this.combinedData.filter(d => d.city != null)
            const cityData = this.cityData;

            const link = []

            cleanData.forEach(function(row){
                const city = cityData.filter(d => d.city === row.city)
                const source = [row.lng, row.lat]
                const target = [city[0].lng, city[0].lat]
                const topush = {type: "LineString", coordinates: [source, target]}
                link.push(topush)
            })

            g.selectAll("myPath")
            .data(link)
            .enter()
            .append("path")
                .attr("d", function(d){ return pathGenerator(d)})
                .style("fill", "none")
                .style("stroke", "#BABABA")
                .style("stroke-width", 1)

            g.selectAll('g')
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

            g.selectAll('g')
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

            g.selectAll('g')
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

            mapChart.call(zooming);
        })

        }
  
      }
  }
</script>

<style scoped>
    #mapdiv {
        display: flex;
        justify-content: flex-end;
        margin: 2em -4.55vw 0 -4.55vw;
        background-color: #0f477e;
        border: solid #c2c2c2;
        border-width: 1px 0 1px 0;
    }
    svg.prmap {
        width: 100%;
        height: 35em;
    }
    svg.legend {
        margin: 2em 0 1em 0;
    }
</style>

