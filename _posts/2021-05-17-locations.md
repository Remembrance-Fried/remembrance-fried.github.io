---
layout: post
title: Funeral 19.05.2021
subtitle: Map of our locations
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/Fried03s.JPG
share-img: /assets/img/path.jpg

---

Most of us are attending Fried's Funeral in different parts of the world. The map is indicative of the far-reaching influence of Fried's teachings, generosity, and love.



<meta charset="utf-8">
<head>
<!-- Load d3.js and the geo projection plugin -->
<script src="https://d3js.org/d3.v4.js"></script>
<script src="https://d3js.org/d3-geo-projection.v2.min.js"></script>
<script src="https://d3js.org/d3-scale-chromatic.v1.min.js"></script>
<style>
  .tooltip {
  position: absolute;
  width: 160px;
  height: auto;
  background: #fdfdfd;
  border-radius:3px;
  pointer-events: auto;
  padding: 2px;       
  font: 11px sans-serif;
  text-align: left;
  color: black;
  padding: 10px;
      }

.tooltip a {
  color: gray;
  text-decoration: none;
  border: none;
}

.tooltip a:hover {
  color: black;
  text-decoration: none;
}

.t_pni {
  margin-bottom: 0px;
  font: 10px sans-serif;
  font-weight: 600;
  color: #1D1D1D;
}

.t_head {
  margin-bottom: 0px;
  font: 10px sans-serif;
  font-weight: 300;
  color: #434343;
}

.t_date {
  margin-top: -8px;
  font: 9px sans-serif;
  -moz-text-decoration-line: underline;
-moz-text-decoration-style: dashed;
  color: #AEAEAE;
}

.t_label {
  font: 9px sans-serif;
  color: #363636;
}

.legend {
  position:absolute;
  margin: 15px;
}
  </style>
</head>

<body>
<!-- Create a div where the circle will take place -->
<div id="map_frame"></div>

<script>
var width = 700,
    height = 400;
    
var svg = d3.select("#map_frame")
          .append("svg")
          .attr("width", width)
          .attr("height", height)
          .style("border","none") 
          .style("background-color", "none")
          .call(d3.zoom()
                 .on("zoom", function () {
          svg.attr("transform", d3.event.transform)
                 })
                 .scaleExtent([1,12])
                 .translateExtent([[0,0],[width,height]])
               
               )
          .append("g");
          

var projection = d3.geoMercator()
    .center([0,20])                // GPS of location to zoom on
    .scale(99)                       // This is like the zoom
    .translate([ width/2, height/2 ])
    .precision(0.1);

function drawRussiaBoundaries() {
      // define the path generator (this should really be at the top)
      var path = d3.geoPath()
          .projection(projection);
    
      // send a JSON request to get the russian boundaries  
      var BoundariesURL = "https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson";
      
      d3.json(BoundariesURL, function(dataGeo) {
        
        // create a group <g> element at the front of the SVG
        // (this ensures that our country boundaries will be below everything else)
// Draw the map
  svg.append("g")
      .selectAll("path")
      .data(dataGeo.features)
      .enter()
      .append("path")
        .attr("fill", "#b8b8b8")
        .attr("d", d3.geoPath()
            .projection(projection)
        )
      .style("stroke", "none")
      .style("opacity", .3)
      });

    }

    drawRussiaBoundaries();

    
// points data CSV path

var pointsData = "https://raw.githubusercontent.com/Remembrance-Fried/remembrance-fried.github.io/master/assets/Augenbroe_pointsv2.csv";

// category colors

var colorScale = d3.scaleOrdinal()
  .domain(["friend", "collaborator", "student"])
  .range(["red", "blue","green"]); //color of categories

// tooltip

var tooltip = d3.select("body").append("div") 
        .attr("class", "tooltip")       
        .style("opacity", 0);

// read CSV and map the dots

d3.csv(pointsData, function(data) {
  data.forEach(function(d) {
    d.lat = +d.lat;
    d.lon = +d.lon;
  });
  
  svg.selectAll("circle")
        .data(data)
        .enter().append("svg:circle")
        .attr("cx", function(d) {var x = projection([d.lon,d.lat])[0]; return x;})
        .attr("cy", function(d) {var y = projection([d.lon,d.lat])[1]; return y;})
        .attr("r", 3)
        .style("fill", function(d){return colorScale(d.connection);})
        .style("stroke", "none")
        .style("stroke-width", 2)
        .style("opacity", 0.5)
        
        .on("mouseover", function(d) {     // tooltip interactive
            //d3.select(this).attr("r",5)
            tooltip.style("opacity", 0.95);   
            tooltip.html('<div class="t_pni">'+d.institute+'</div>'//+ // agency
                         //'<div class="t_head">'+d.institute+'</div>'
                         ) // date
            .attr("id","tool")
            .style("left", (d3.event.pageX+0) + "px")   
            .style("top", (d3.event.pageY-30) + "px")
            .style("display", "block")
            d3.selectAll("#tool")
            .on("mouseover", function(d) {
              tooltip.style("opacity", 0.95)
              .style("display", "block");
            });
              d3.selectAll("#tool").on("mouseout", function(d) {
              tooltip.style("opacity", 0)
              .style("display", "none");
            });
          });
});
</script>
</body>


