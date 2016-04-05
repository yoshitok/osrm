# osrm R package

[![Version](http://www.r-pkg.org/badges/version/osrm)](https://CRAN.R-project.org/package=osrm/)
![](http://cranlogs.r-pkg.org/badges/osrm?color=brightgreen)
[![Travis-CI Build Status](https://travis-ci.org/rCarto/osrm.svg?branch=master)](https://travis-ci.org/rCarto/osrm)  

***Interface Between R and the OpenStreetMap-Based Routing Service [OSRM](http://project-osrm.org/)***

![](http://f.hypotheses.org/wp-content/blogs.dir/1909/files/2015/10/route1.png)

## Description
OSRM is a routing service based on OpenStreetMap data. See <http://project-osrm.org/> for more information. A public API exists but one can run its own instance. This package allows to compute distance (travel time and kilometric distance) between points and travel time matrices.

This package relies on the usage of a running OSRM service (tested with version 4.9.0 of the OSRM API). 
By default this service is the OSRM public API (http://router.project-osrm.org/). To change the OSRM server, change the osrm.server option:
`options(osrm.server = "http://address.of.the.server/")`

## Features

* `osrmTable` Get travel time matrices between points.

* `osrmViaRoute` Get travel time and travel distance between two points.

* `osrmViaRouteGeom` Get the travel geometry between two points.

* `osrmTripGeom` Get the travel geometry between multiple unordered points.

* `osrmIsochrone` Get a SpatialPolygonsDataFrame of isochrones.


## Demo

### `osrmTable`

```r
library(osrm)
# Load data
data("com")
# Travel time matrix
distCom <- osrmTable(loc = com[1:50, c("comm_id","lat","lon")])
# First 5 rows and columns
distCom$distance_table[1:5,1:5]
```
<table border=1>
<tr> <th>  </th> <th> FR3162292119 </th> <th> FR3162212035 </th> <th> BE241011 </th> <th> BE241024 </th> <th> FR3162270520 </th>  </tr>
<tr> <td align="right"> FR3162292119 </td> <td align="right"> 0.00 </td> <td align="right"> 5.50 </td> <td align="right"> 95.10 </td> <td align="right"> 91.60 </td> <td align="right"> 7.50 </td> </tr>
<tr> <td align="right"> FR3162212035 </td> <td align="right"> 4.90 </td> <td align="right"> 0.00 </td> <td align="right"> 98.30 </td> <td align="right"> 94.70 </td> <td align="right"> 7.10 </td> </tr>
<tr> <td align="right"> BE241011 </td> <td align="right"> 94.20 </td> <td align="right"> 97.30 </td> <td align="right"> 0.00 </td> <td align="right"> 10.40 </td> <td align="right"> 93.50 </td> </tr>
<tr> <td align="right"> BE241024 </td> <td align="right"> 90.80 </td> <td align="right"> 93.80 </td> <td align="right"> 10.40 </td> <td align="right"> 0.00 </td> <td align="right"> 90.00 </td> </tr>
<tr> <td align="right"> FR3162270520 </td> <td align="right"> 7.00 </td> <td align="right"> 6.90 </td> <td align="right"> 95.00 </td> <td align="right"> 91.50 </td> <td align="right"> 0.00 </td> </tr>
</table>


### `osrmViaRoute`

```r
library(osrm)
# Load data
data("com")
# Time and Distance between 2 points
route <- osrmViaroute(src = com[1,c("lat","lon")],
                      dst = com[15,c("lat","lon")] )
# Time travel distance (min & km)
route
```
<table border=1>
<tr> <th>  </th> <th> route </th>  </tr>
<tr> <td align="right"> total_time </td> <td align="right"> 35.75 </td> </tr>
<tr> <td align="right"> total_distance </td> <td align="right"> 38.04 </td> </tr>
</table>


### `osrmViaRouteGeom`

```r
library(osrm)
# Load data
data("com")

# Travel path between SpatialPointsDataFrame
routeGeom <- osrmViarouteGeom(src = src[1,], 
                              dst = dst[1,], 
                              sp = TRUE)
plot(routeGeom, lty = 2, lwd = 2)
plot(src[1,], pch = 20, col = "green", cex = 3, add = TRUE)             
plot(dst[1,], pch = 20, col = "red", cex = 3, add = TRUE) 
```
![](http://rgeomatic.hypotheses.org/files/2016/04/viageom.png)


### `osrmTripGeom`

```r
library(osrm)
# Load data
data("com")

# Get a trip with a SpatialPointsDataFrame
trips <- osrmTripGeom(loc = src)

# Map
if(require("cartography")){
  osm <- getTiles(spdf = trips[[1]]$trip, crop = TRUE)
  tilesLayer(osm)
  plot(src, pch = 20, col = "red", cex = 2, add = TRUE)
  plot(trips[[1]]$trip, add = TRUE, lwd=2)
}

```

![](http://rgeomatic.hypotheses.org/files/2016/04/trips.png)

### `osrmTripGeom`

```r
library(osrm)
# Load data
data("com")

# Get isochones with a SpatialPointsDataFrame, custom breaks
iso <- osrmIsochrone(loc = src[6,], breaks = seq(from = 0,to = 30, by = 5))

# Map
if(require("cartography")){
  osm <- getTiles(spdf = iso, crop = TRUE)
  tilesLayer(osm)
  breaks <- sort(c(unique(iso$min), max(iso$max)))
  pal <- paste(carto.pal("taupe.pal", length(breaks)-1), "95", sep="")
  
  cartography::choroLayer(spdf = iso, df = iso@data,
                          var = "center", breaks = breaks,
                          border = "grey50", col = pal,
                          legend.pos = "topleft",legend.frame = TRUE, 
                          legend.title.txt = "Isochrones\n(min)", 
                          add = TRUE)
  plot(src[6,], add=T)
}
```
![](http://rgeomatic.hypotheses.org/files/2016/04/iso.png)


## Installation

* Development version on GitHub
```{r}
require(devtools)
devtools::install_github("rCarto/osrm")
```

* Stable version on [CRAN](https://CRAN.R-project.org/package=osrm/)
```{r}
install.packages("osrm")
```









