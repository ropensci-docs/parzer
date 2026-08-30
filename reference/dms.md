# extract degree, minutes, and seconds

extract degree, minutes, and seconds

## Usage

``` r
pz_degree(lon = NULL, lat = NULL)

pz_minute(lon = NULL, lat = NULL)

pz_second(lon = NULL, lat = NULL)

# S3 method for class 'pz'
print(x, ...)

pz_d(x)

pz_m(x)

pz_s(x)

# S3 method for class 'pz'
e1 + e2

# S3 method for class 'pz'
e1 - e2

# S3 method for class 'pz'
e1/e2

# S3 method for class 'pz'
e1 * e2
```

## Arguments

- lon, lat:

  (numeric/integer/character) one or more longitude or latitude values.
  values are internally validated. only one of lon or lat accepted

- x:

  (integer) an integer representing a degree, minute or second

- ...:

  print dots

- e1, e2:

  objects of class pz, from using `pz_d()`, `pz_m()`, or `pz_s()`

## Value

`pz_degree`: integer, `pz_minute`: integer, `pz_second`: numeric,
`pz_d`: numeric, `pz_m`: numeric, `pz_s`: numeric (adding/subtracting
these also gives numeric)

## Details

Mathematics operators are exported for `+`, `-`, `/`, and `*`, but `/`
and `*` are only exported with a stop message to say it's not supported;
otherwise you'd be allow to divide degrees by minutes, leading to
nonsense.

## Examples

``` r
# extract parts of a coordinate value
pz_degree(-45.23323)
#> [1] -45
pz_minute(-45.23323)
#> [1] 13
pz_second(-45.23323)
#> [1] 59.628

pz_degree(lon = 178.23423)
#> [1] 178
pz_minute(lon = 178.23423)
#> [1] 14
pz_second(lon = 178.23423)
#> [1] 3.228
if (FALSE) { # \dontrun{
pz_degree(lat = c(45.23323, "40:25:6N", "40° 25´ 5.994 S"))
pz_minute(lat = c(45.23323, "40:25:6N", "40° 25´ 5.994 S"))
pz_second(lat = c(45.23323, "40:25:6N", "40° 25´ 5.994 S"))

# invalid
pz_degree(445.23323)

# add or subtract
pz_d(31)
pz_m(44)
pz_s(3)
pz_d(31) + pz_m(44)
pz_d(-31) - pz_m(44)
pz_d(-31) + pz_m(44) + pz_s(59)
pz_d(31) - pz_m(44) + pz_s(59)
pz_d(-121) + pz_m(1) + pz_s(33)
unclass(pz_d(31) + pz_m(44) + pz_s(59))
} # }
```
