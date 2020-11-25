<template>
  <h1>Map.vue</h1>
  <div id="mapdiv">
        
	</div>
</template>

<script>
import * as d3 from "d3"
import * as topojson from "topojson"

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
        const mapChart = d3.select("#mapdiv")
            .append("svg")
                .attr('class', 'map')
                .attr("width", this.width)
                .attr("height", this.height)

        const g = mapChart.append("g")
                .attr("transform", "translate(300,400), scale(.45)");
        
        const projection = d3.geoMercator().scale(100000).center([5.116667,52.17000])
        const pathGenerator = d3.geoPath().projection(projection)
        
        d3.json('https://cartomap.github.io/nl/wgs84/landsdeel_2020.topojson').then(data => {
            const gemeentes = topojson.feature(data, data.objects.landsdeel_2020)
             g.selectAll('path')
            .data(gemeentes.features)
            .enter()
            .append('path')
            .attr('d', pathGenerator)
            .attr('fill', '#F2F2F2')

            const zoom = d3.zoom()
            .scaleExtent([-2, 5])
            .on('zoom',  (e) => {
            g.attr('transform', e.transform)
            });

            const div = d3.select("body").append("div")
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

            mapChart.call(zoom);
        })

        }
  
      }
  }
</script>

<style scoped>
#mapdiv {
  display: flex;
  justify-content: flex-end;
  margin-right: -2.55vw;
}
</style>

