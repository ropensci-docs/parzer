# Introduction to the parzer package

`parzer` parses messy coordinates

You may get data from a published study or a colleague, and the
coordinates may be in some messy character format that you’d like to
clean up to have all decimal degree numeric data.

`parzer` API:

- `parse_hemisphere`
- `parse_lat`
- `parse_lon`
- `parse_lon_lat`
- `parse_parts_lat`
- `parse_parts_lon`
- `pz_d`
- `pz_degree`
- `pz_m`
- `pz_minute`
- `pz_s`
- `pz_second`

## Install

Stable version

``` r

install.packages("parzer")
```

Development version

``` r

remotes::install_github("ropensci/parzer")
```

## Parse

``` r

library(parzer)
```

### Latitudes:

``` r

parse_lat("45N54.2356")
## [1] 45.90393
parse_lat("-45.98739874")
## [1] -45.9874
parse_lat("40.123°")
## [1] 40.123
parse_lat("40.123N")
## [1] 40.123
parse_lat("N45 04.25764")
## [1] 45.07096

# Invalid values -> NaN
parse_lat("191.89")
## Warning in base::.Call(...): not within -90/90 range, got: 191.89
##   check that you did not invert lon and lat
## [1] NA

# Many inputs
x <- c("40.123°", "40.123N", "11.89", 12, "N45 04.25764")
parse_lat(x)
## [1] 40.12300 40.12300 11.89000 12.00000 45.07096

# Many inputs but with problems
x_warnings <- c("40.123°", "40.123N74.123W", "191.89", 12, "N45 04.25764")
parse_lat(x_warnings)
## Warning in base::.Call(...): invalid direction letter, got: 40.123n74.123w
## Warning in base::.Call(...): not within -90/90 range, got: 191.89
##   check that you did not invert lon and lat
## [1] 40.12300       NA       NA 12.00000 45.07096
```

### Longitudes:

``` r

parse_lon("45W54.2356")
## [1] -45.90393
parse_lon("-45.98739874")
## [1] -45.9874
parse_lon("40.123°")
## [1] 40.123
parse_lon("74.123W")
## [1] -74.123
parse_lon("W45 04.25764")
## [1] -45.07096

# Invalid values
parse_lon("361")
## Warning in base::.Call(...): not within -180/360 range, got: 361
## [1] NA

# Many inputs
x <- c("45W54.2356", "181", 45, 45.234234, "-45.98739874")
parse_lon(x)
## [1] -45.90393 181.00000  45.00000  45.23423 -45.98740
```

### Both longitudes and latitudes at the same time:

``` r

lons <- c("45W54.2356", "181", 45, 45.234234, "-45.98739874")
lats <- c("40.123°", "40.123N", 40, 12, "N45 04.25764")
parse_lon_lat(lons, lats)
```

            lon      lat
    1 -45.90393 40.12300
    2 181.00000 40.12300
    3  45.00000 40.00000
    4  45.23423 12.00000
    5 -45.98740 45.07096

### Both longitudes and latitudes in the same string:

``` r

lat_lon_strings <- c(
  "40.123°, 45W54.2356",
  "N40.123 E181.456",
  "40, 45",
  "12.9786 45.234234",
  "N45 04.25764, -45.98739874W"
)

parse_llstr(lat_lon_strings)
```

           lat       lon
    1 40.12300 -45.90393
    2 40.12300 181.45600
    3 40.00000  45.00000
    4 12.97860  45.23423
    5 45.07096 -45.98740

### Parse into degree, min, sec parts:

``` r

parse_parts_lat("45N54.2356")
##   deg min    sec
## 1  45  54 14.136
parse_parts_lon("-74.6411133")
##   deg min      sec
## 1 -74  38 28.00788

# Many inputs
x <- c("40.123°", "40.123W", "191.89", 12, "E45 04.25764")
parse_parts_lon(x)
##   deg min     sec
## 1  40   7 22.8000
## 2 -40   7 22.8000
## 3 191  53 24.0000
## 4  12   0  0.0000
## 5  45   4 15.4584

# Also handles invalid inputs gracefully
x_warning <- c("40.123°", "40.123N74.123W", "191.89", 12, "N45 04.25764")
parse_parts_lon(x_warning)
## Warning in base::.Call(...): invalid direction letter, got: 40.123n74.123w
## Warning in base::.Call(...): invalid direction letter, got: n45 04.25764
##   deg min  sec
## 1  40   7 22.8
## 2  NA  NA   NA
## 3 191  53 24.0
## 4  12   0  0.0
## 5  NA  NA   NA
```

### Get degree, minutes, or seconds separately:

``` r

coords <- c(45.23323, "40:25:6N", "40° 25´ 5.994\" N")
pz_degree(lat = coords)
## [1] 45 40 40
pz_minute(lat = coords)
## [1] 13 25 25
pz_second(lat = coords)
## [1] 59.628  6.000  5.994

coords <- c(15.23323, "40:25:6E", "192° 25´ 5.994\" E")
pz_degree(lon = coords)
## [1]  15  40 192
pz_minute(lon = coords)
## [1] 13 25 25
pz_second(lon = coords)
## [1] 59.628  6.000  5.994
```

### Add or subtract degrees, minutes, or seconds:

``` r

pz_d(31)
## 31
pz_d(31) + pz_m(44)
## 31.73333
pz_d(31) - pz_m(44)
## 30.26667
pz_d(31) + pz_m(44) + pz_s(59)
## 31.74972
pz_d(-121) + pz_m(1) + pz_s(33)
## -120.9742
```

### Get hemisphere from lat/lon coords:

``` r

parse_hemisphere("74.123E", "45N54.2356")
## [1] "NE"
parse_hemisphere("-120", "40.4183318")
## [1] "NW"
parse_hemisphere("-120", "-40.4183318")
## [1] "SW"
parse_hemisphere("120", "-40.4183318")
## [1] "SE"
```
