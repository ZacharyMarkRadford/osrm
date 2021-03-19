
# osrm R package

[![Version](http://www.r-pkg.org/badges/version/osrm)](https://CRAN.R-project.org/package=osrm/)
![](http://cranlogs.r-pkg.org/badges/osrm?color=brightgreen) [![R build
status](https://github.com/riatelab/osrm/workflows/R-CMD-check/badge.svg)](https://github.com/riatelab/osrm/actions)
[![codecov](https://codecov.io/gh/riatelab/osrm/branch/master/graph/badge.svg?token=JOJNuBCH9M)](https://codecov.io/gh/riatelab/osrm)

***Interface Between R and the OpenStreetMap-Based Routing Service
[OSRM](http://project-osrm.org/)***

![](https://raw.githubusercontent.com/riatelab/osrm/master/img/cover.png)

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
| 440338666  |       0.0 |      21.4 |      34.1 |       19.5 |      11.7 |
| 538057637  |      22.8 |       0.0 |      42.8 |       16.1 |      20.7 |
| 977657079  |      34.1 |      43.3 |       0.0 |       31.2 |      27.4 |
| 3770254015 |      22.3 |      15.3 |      30.7 |        0.0 |      12.7 |
| 364363337  |      12.0 |      20.2 |      26.8 |       12.0 |       0.0 |

</small>

### `osrmRoute`

``` r
library(sf)
library(maptiles)
library(mapsf)
# Route
route <- osrmRoute(src = apotheke.sf[74,], dst = apotheke.sf[55,],
                   overview = "full", returnclass = "sf")
# Display
osm <- get_tiles(x = route, crop = TRUE, zoom = 13)
png("img/route.png", width = 693, height = 263)
par(mar = c(0,0,0,0))
plot_tiles(osm)
mf_map(route, lwd = 4, add = TRUE, col = "black")
mf_map(route, lwd = 1, col = "white", add = TRUE)
mf_map(apotheke.sf[c(74,55),], pch = 20, col = "red", add = TRUE)
mf_credits(get_credit("OpenStreetMap"), pos = "bottomright", cex = .9)
dev.off()
```

![](https://raw.githubusercontent.com/riatelab/osrm/master/img/route.png)

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
mf_map(trip, col = "black", lwd = 4, add = TRUE )
mf_map(trip, col = c("red", "white"), lwd = 1, add = TRUE)
mf_map(apotheke.sf[10:20,], pch = 21, col = "red", cex = 1.5, add = TRUE)
mf_credits(get_credit("OpenStreetMap"), pos = "bottomright", cex = .9)
dev.off()
```

![](https://raw.githubusercontent.com/riatelab/osrm/master/img/trip.png)

### `osrmIsochrone`

``` r
bks <- seq(from = 0, to = 14, by = 2)
iso <- osrmIsochrone(loc = apotheke.sf[87,], returnclass="sf",
                     breaks = bks, res = 70)
osm3 <- get_tiles(x = iso, crop = TRUE, zoom = 12)
cols <- hcl.colors(n = 7, palette = "Emrld", alpha = 0.75, rev = F)
png("img/iso.png", width = 604, height = 595)
par(mar = c(0,0,0,0))
plot_tiles(osm3)
mf_theme(mar = c(0,0,0,0))
mf_map(x = iso, var = "center", type = "choro", 
       breaks = bks, border = NA, pal = cols,
       leg_pos = "topleft", leg_frame = T,
       leg_title = "Isochrones\n(min)",
       leg_title_cex = 1, leg_val_cex = .8,
       add = TRUE)
mf_map(apotheke.sf[87,], pch = 21, col = "red", 
       cex = 1.5, add=TRUE)
mf_credits(get_credit("OpenStreetMap"), cex = .9)
dev.off()
```

![](https://raw.githubusercontent.com/riatelab/osrm/master/img/iso.png)

## Installation

-   Development version on GitHub

``` r
remotes::install_github("riatelab/osrm")
```

-   Stable version on [CRAN](https://CRAN.R-project.org/package=osrm/)

``` r
install.packages("osrm")
```

## Community Guidelines

One can contribute to the package through [pull
requests](https://github.com/riatelab/osrm/pulls) and report issues or
ask questions [here](https://github.com/riatelab/osrm/issues).
