# Parzer use cases

## Use case: working with spatial R packages

``` r

if (!requireNamespace("sf")) install.packages("sf")
if (!requireNamespace("leaflet")) install.packages("leaflet")
```

``` r

library(parzer)
```

One may find themselves having to clean up messy coordinates as part of
their project/work/etc. How does this look when fit into a workflow
going all the way to visualization?

Let’s say you have the following messy coordinates that you’ve compiled
from different places, leading to a variety of messy formats:

``` r

lats <- c(
  "46.4183",
  "46.4383° N",
  "46.5683° N",
  "46° 27´ 5.4\" N",
  "46° 25.56’",
  "N46°24’4.333"
)
lons <- c(
  "25.7391",
  "E25°34’6.4533",
  "25.3391° E",
  "25.8391° E",
  "25° 35.56’",
  "E25°34’4.333"
)
```

Parse messy coordinates:

``` r

dat <- data.frame(
  longitude = parse_lon(lons),
  latitude = parse_lat(lats)
)
dat
##   longitude latitude
## 1  25.73910  46.4183
## 2  25.56846  46.4383
## 3  25.33910  46.5683
## 4  25.83910  46.4515
## 5  25.59267  46.4260
## 6  25.56787  46.4012
```

Combine coordinates with other data.

``` r

dat$shape <- c("round", "square", "triangle", "round", "square", "square")
dat$color <- c("blue", "yellow", "green", "red", "green", "yellow")
dat
##   longitude latitude    shape  color
## 1  25.73910  46.4183    round   blue
## 2  25.56846  46.4383   square yellow
## 3  25.33910  46.5683 triangle  green
## 4  25.83910  46.4515    round    red
## 5  25.59267  46.4260   square  green
## 6  25.56787  46.4012   square yellow
```

Coerce to an `sf` object

``` r

datsf <- sf::st_as_sf(dat, coords = c("longitude", "latitude"))
datsf
## Simple feature collection with 6 features and 2 fields
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: 25.3391 ymin: 46.4012 xmax: 25.8391 ymax: 46.5683
## CRS:           NA
##      shape  color                 geometry
## 1    round   blue  POINT (25.7391 46.4183)
## 2   square yellow POINT (25.56846 46.4383)
## 3 triangle  green  POINT (25.3391 46.5683)
## 4    round    red  POINT (25.8391 46.4515)
## 5   square  green  POINT (25.59267 46.426)
## 6   square yellow POINT (25.56787 46.4012)
```

Calculate the center of the plot view

``` r

center_lon <- mean(dat$longitude)
center_lat <- mean(dat$latitude)
```

Plot data using the `leaflet` package

``` r

library("leaflet")
leaflet() |>
  addTiles() |>
  addMarkers(data = datsf) |>
  setView(center_lon, center_lat, zoom = 10)
```

Figure 1

We’d like to have data only for a certain area, e.g., a political
boundary or a park boundary. We can clip the data to a bounding box
using
[`sf::st_crop()`](https://r-spatial.github.io/sf/reference/st_crop.html).

First, define the bounding box, and visualize:

``` r

bbox <- c(
  xmin = 25.42813, ymin = 46.39455,
  xmax = 25.68769, ymax = 46.60346
)
leaflet() |>
  addTiles() |>
  addRectangles(bbox[["xmin"]], bbox[["ymin"]], bbox[["xmax"]], bbox[["ymax"]]) |>
  setView(center_lon, center_lat, zoom = 10)
```

Figure 2

Crop the data to the bounding box:

``` r

datsf_c <- sf::st_crop(datsf, bbox)
```

    Warning: attribute variables are assumed to be spatially constant throughout
    all geometries

Plot the new data:

``` r

leaflet() |>
  addTiles() |>
  addMarkers(data = datsf_c) |>
  setView(center_lon, center_lat, zoom = 10)
```

Figure 3
