# Changelog

## parzer 0.4.4

CRAN release: 2025-07-24

#### BUG FIX

- Fixed a bug in longitude conversion where “74E5423” would be converted
  to NA while “74W5423” was correctly converted.
- Improved C++ management of NA values.

## parzer 0.4.3

CRAN release: 2025-05-29

#### BUG FIX

- Removal of unnecessary files from the package.

## parzer 0.4.2

CRAN release: 2025-05-19

#### MINOR IMPROVEMENTS

- C++ code was improved by replacing push.backs with direct assignations
  in vectors and also by passing arguments by reference where possible.
- Dependence on Rcpp was reduced

#### BUG FIX

- having spaces at the beginning of a string could lead to the
  disappearing of the negative sign

## parzer 0.4.1

CRAN release: 2021-12-20

#### MINOR IMPROVEMENTS

- documentation and package description describe more clearly `parzer`
  core objective of parsing messy coordinates in character strings to
  convert them to decimal numeric values. Suggestion and work by
  [@robitalec](https://github.com/robitalec)

#### ACKNOWLEDGEMENTS CHANGES

- new contributors to the package:
  [@robitalec](https://github.com/robitalec),
  [@maelle](https://github.com/maelle) and
  [@yutannihilation](https://github.com/yutannihilation)
- new maintainer: [@AlbanSagouis](https://github.com/AlbanSagouis)

## parzer 0.4.0

CRAN release: 2021-02-16

#### MINOR IMPROVEMENTS

- performance improvement for internal function `scrub()`, used in most
  exported functions in parzer
  ([\#30](https://github.com/ropensci/parzer/issues/30)) work by
  [@AlbanSagouis](https://github.com/AlbanSagouis)
- work around for non-UTF8 MBCS locales: now all exported functions go
  through a modified
  [`.Call()`](https://rdrr.io/r/base/CallExternal.html) in which we use
  [`withr::with_locale()`](https://withr.r-lib.org/reference/with_locale.html)
  if the user is on a Windows operating system
  ([\#31](https://github.com/ropensci/parzer/issues/31))
  ([\#32](https://github.com/ropensci/parzer/issues/32)) work by
  [@yutannihilation](https://github.com/yutannihilation)

## parzer 0.3.0

CRAN release: 2020-10-13

#### BUG FIXES

- fix problem in
  [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md):
  on older R versions where `stringsAsFactors=TRUE` by default this
  function was returning strings as factors from an internal function
  that caused a problem in a subsequent step in the function
  ([\#29](https://github.com/ropensci/parzer/issues/29))

## parzer 0.2.0

CRAN release: 2020-10-07

#### NEW FEATURES

- new contributor to the package
  [@AlbanSagouis](https://github.com/AlbanSagouis)
- gains new function
  [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  to parse a string that contains both latitude and longitude
  ([\#3](https://github.com/ropensci/parzer/issues/3))
  ([\#24](https://github.com/ropensci/parzer/issues/24))
  ([\#26](https://github.com/ropensci/parzer/issues/26))
  ([\#28](https://github.com/ropensci/parzer/issues/28)) work by
  [@AlbanSagouis](https://github.com/AlbanSagouis)

#### MINOR IMPROVEMENTS

- updated `scrub()` internal function that strips certain characters to
  include more things to scrub
  ([\#25](https://github.com/ropensci/parzer/issues/25)) work by
  [@AlbanSagouis](https://github.com/AlbanSagouis)

## parzer 0.1.4

CRAN release: 2020-03-29

#### MINOR IMPROVEMENTS

- add support to internal function for additional degree like symbols
  ([\#21](https://github.com/ropensci/parzer/issues/21))
- fix issue with
  [`parse_parts_lat()`](https://docs.ropensci.org/parzer/reference/parse_parts.md)/[`parse_parts_lon()`](https://docs.ropensci.org/parzer/reference/parse_parts.md)
  functions where an NA was causing warnings on the cpp side; on cpp
  side, now check for NA and return list of NAs instead of NAs passing
  through other code
  ([\#23](https://github.com/ropensci/parzer/issues/23))

## parzer 0.1.0

CRAN release: 2020-03-19

#### NEW FEATURES

- Released to CRAN.
