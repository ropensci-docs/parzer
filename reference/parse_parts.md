# parse coordinates into degrees, minutes and seconds

parse coordinates into degrees, minutes and seconds

## Usage

``` r
parse_parts_lon(str)

parse_parts_lat(str)
```

## Arguments

- str:

  (character) string including longitude or latitude

## Value

data.frame with columns for:

- deg (integer)

- min (integer)

- sec (numeric)

NA/NaN given upon error

## Examples

``` r
parse_parts_lon("140.4183318")
#>   deg min     sec
#> 1 140  25 5.99448
if (FALSE) { # \dontrun{
parse_parts_lon("174.6411133")
parse_parts_lon("-45.98739874")
parse_parts_lon("40.123W")

parse_parts_lat("45N54.2356")
parse_parts_lat("40.4183318")
parse_parts_lat("-74.6411133")
parse_parts_lat("-45.98739874")
parse_parts_lat("40.123N")
parse_parts_lat("N40°25’5.994")

# not working, needs format input
parse_parts_lat("N455698735")

# multiple
x <- c("40.123°", "40.123N74.123W", "191.89", 12, "N45 04.25764")
parse_parts_lat(x)
system.time(parse_parts_lat(rep(x, 10^2)))
} # }
```
