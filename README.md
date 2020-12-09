
# osrm R package

[![Version](http://www.r-pkg.org/badges/version/osrm)](https://CRAN.R-project.org/package=osrm/)
![](http://cranlogs.r-pkg.org/badges/osrm?color=brightgreen) [![R build
status](https://github.com/rCarto/osrm/workflows/R-CMD-check/badge.svg)](https://github.com/rCarto/osrm/actions)
[![codecov](https://codecov.io/gh/rCarto/osrm/branch/master/graph/badge.svg?token=JOJNuBCH9M)](https://codecov.io/gh/rCarto/osrm)

***Interface Between R and the OpenStreetMap-Based Routing Service
[OSRM](http://project-osrm.org/)***

![](https://raw.githubusercontent.com/rCarto/osrm/master/img/cover.png)

## Description

OSRM is a routing service based on OpenStreetMap data. See
<http://project-osrm.org/> for more information. This package allows to
compute routes, trips, isochrones and travel distances matrices (travel
time and kilometric distance).

This package relies on the usage of a running OSRM service (tested with
v5.23.0 of OSRM).

You can run your own instance of OSRM following guidelines provided
[here](https://github.com/Project-OSRM/osrm-backend). The simplest
solution is probably the one based on [docker
containers](https://github.com/Project-OSRM/osrm-backend#using-docker).

:warning: **You must be careful using the OSRM demo server and read the
[*about* page](https://routing.openstreetmap.de/about.html) of the
service**:  
&gt; [One request per second max. No scraping, no heavy
usage.](https://routing.openstreetmap.de/about.html)

:heavy\_exclamation\_mark: **To consider when using OSRM**:  
&gt; [“Most of the previously active core devs have either moved on to
new roles, or are simply busy on different projects
(…)”](https://github.com/Project-OSRM/osrm-backend/issues/5463)

## Features

-   `osrmTable` Get travel time matrices between points.

-   `osrmRoute` Get the shortest path between two points.

-   `osrmTrip` Get the travel geometry between multiple unordered
    points.

-   `osrmIsochrone` Get polygons of isochrones.

## Demo

### `osrmTable`

``` r
library(osrm)
```

``` r
data("berlin")
# Travel time matrix
distA <- osrmTable(loc = apotheke.sf[1:5,])
distA$durations
```

<small>

|            | 440338666 | 538057637 | 977657079 | 3770254015 | 364363337 |
|:-----------|----------:|----------:|----------:|-----------:|----------:|
| 440338666  |       0.0 |      21.5 |      34.1 |       19.7 |      11.8 |
| 538057637  |      22.9 |       0.0 |      42.0 |       16.1 |      20.7 |
| 977657079  |      34.0 |      42.6 |       0.0 |       30.5 |      27.3 |
| 3770254015 |      22.4 |      15.3 |      29.7 |        0.0 |      12.7 |
| 364363337  |      12.1 |      20.2 |      26.7 |       12.0 |       0.0 |

</small>

### `osrmRoute`

``` r
library(sf)
library(maptiles)
# Route
route <- osrmRoute(src = apotheke.sf[74,], dst = apotheke.sf[55,],
                   overview = "full", returnclass = "sf")
# Display
osm <- get_tiles(x = route, crop = TRUE, zoom = 13)
png("img/route.png", width = 693, height = 263)
par(mar = c(0,0,0,0))
plot_tiles(osm)
plot(st_geometry(route), lwd = 4, add = TRUE)
plot(st_geometry(route), lwd = 1, col = "white", add = TRUE)
plot(st_geometry(apotheke.sf[c(74,55),]), pch = 20, col = "red", add = TRUE)
mtext(get_credit("OpenStreetMap"), side = 1, line = -1, cex = .9, adj = .99)
dev.off()
```

![](https://raw.githubusercontent.com/rCarto/osrm/master/img/route.png)

### `osrmTrip`

``` r
# Trip 
trips <- osrmTrip(loc = apotheke.sf[10:20,], returnclass="sf")
trip <- trips[[1]]$trip
# Display
osm2 <- get_tiles(x = trip, crop = TRUE, zoom = 11)
png("img/trip.png", width = 499, height = 420)
par(mar = c(0,0,0,0))
plot_tiles(osm2)
plot(st_geometry(trip), col = "black", lwd = 4, add = TRUE )
plot(st_geometry(trip), col = c("red", "white"), lwd = 1, add=TRUE)
plot(st_geometry(apotheke.sf[10:20,]), pch = 21, bg = "red", cex = 1.5, add=TRUE)
mtext(get_credit("OpenStreetMap"), side = 1, line = -1, cex = .9, adj = .99)
dev.off()
```

![](https://raw.githubusercontent.com/rCarto/osrm/master/img/trip.png)

### `osrmIsochrone`

``` r
iso <- osrmIsochrone(loc = apotheke.sf[87,], returnclass="sf",
                     breaks = seq(from = 0, to = 14, by = 2), res = 70)
osm3 <- get_tiles(x = iso, crop = TRUE, zoom = 12)
bks <- sort(c(unique(iso$min), max(iso$max)))
library(cartography)
cols <- paste0(carto.pal("turquoise.pal", n1 = length(bks)-1), 'BF')
png("img/iso.png", width = 604, height = 595)
par(mar = c(0,0,0,0))
plot_tiles(osm3)
choroLayer(x = iso, var = "center", breaks = bks,
           border = NA, col = cols,
           legend.pos = "topleft",legend.frame = TRUE,
           legend.title.txt = "Isochrones\n(min)",
           legend.title.cex = 1, legend.values.cex = .8,
           add = TRUE)
plot(st_geometry(apotheke.sf[87,]), pch = 21, bg = "red", 
     cex = 1.5, add=TRUE)
mtext(get_credit("OpenStreetMap"), side = 1, line = -1, cex = .9, adj = .99)
dev.off()
```

![](https://raw.githubusercontent.com/rCarto/osrm/master/img/iso.png)

## Installation

-   Development version on GitHub

``` r
remotes::install_github("rCarto/osrm")
```

-   Stable version on [CRAN](https://CRAN.R-project.org/package=osrm/)

``` r
install.packages("osrm")
```

## Community Guidelines

One can contribute to the package through [pull
requests](https://github.com/rCarto/osrm/pulls) and report issues or ask
questions [here](https://github.com/rCarto/osrm/issues).
