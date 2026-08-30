# get hemisphere from long/lat coordinates

BEWARE: EXPERIMENTAL

## Usage

``` r
parse_hemisphere(lon, lat)
```

## Arguments

- lon:

  (character/numeric/integer) one or more longitude values

- lat:

  (character/numeric/integer) one or more latitude values

## Value

character vector of quadrants, one of: NE, NW, SE, SW. if one of the
coordinate values is invalid, and one is valid, you get a length 1
string. if both coordinate values are bad, you get a zero length string.

Warnings are thrown on invalid values

## Details

length(lon) == length(lat)

## Examples

``` r
# NE
parse_hemisphere("74.123E", "45N54.2356")
#> [1] "NE"
if (FALSE) { # \dontrun{
# NW
parse_hemisphere(-120, 40.4183318)
# SW
parse_hemisphere(-120, -40.4183318)
# SE
parse_hemisphere(120, -40.4183318)

# bad inputs, get one of the two strings
parse_hemisphere(-181, -40.4183318)
parse_hemisphere(-120, -192.4183318)
} # }
```
