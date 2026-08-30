# parse string with lat and lon together

parse string with lat and lon together

## Usage

``` r
parse_llstr(str)
```

## Arguments

- str:

  (character) string with latitude and longitude, one or more in a
  vector.

## Value

A data.frame with parsed latitude and longitude in decimal degrees.

## Examples

``` r
parse_llstr("N 04.1683, E 101.5823")
#>      lat      lon
#> 1 4.1683 101.5823
parse_llstr("N04.82344, E101.61320")
#>       lat      lon
#> 1 4.82344 101.6132
parse_llstr("N 04.25164, E 101.70695")
#>       lat     lon
#> 1 4.25164 101.707
parse_llstr("N05.03062, E101.75172")
#>       lat      lon
#> 1 5.03062 101.7517
parse_llstr("N05.03062,E101.75172")
#>       lat      lon
#> 1 5.03062 101.7517
parse_llstr("N4.9196, E101.345")
#>      lat     lon
#> 1 4.9196 101.345
parse_llstr("N4.9196, E101.346")
#>      lat     lon
#> 1 4.9196 101.346
parse_llstr("N4.9196, E101.347")
#>      lat     lon
#> 1 4.9196 101.347
# no comma
parse_llstr("N4.9196 E101.347")
#>      lat     lon
#> 1 4.9196 101.347
# no space
parse_llstr("N4.9196E101.347")
#>   lat lon
#> 1  NA  NA

# DMS
parse_llstr("N4 51'36\", E101 34'7\"")
#>    lat      lon
#> 1 4.86 101.5686
parse_llstr(c("4 51'36\"S, 101 34'7\"W", "N4 51'36\", E101 34'7\""))
#>     lat       lon
#> 1 -4.86 -101.5686
#> 2  4.86  101.5686
```
