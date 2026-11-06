# Changelog

## parzer 0.5.0

#### BUG FIX

- [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md)
  and
  [`parse_lon()`](https://docs.ropensci.org/parzer/reference/parse_lon.md)
  (and the functions built on them) now return `NA` with a warning when
  minutes or seconds are not within 0/60, instead of silently converting
  them, e.g. “10°30,12345”
  ([\#60](https://github.com/ropensci/parzer/issues/60)). Negative
  minutes or seconds, and a decimal degree or minute followed by another
  slot (e.g. “45.5 30”), now also give `NA` with a warning. A comma is a
  separator, not a decimal mark; this is now documented.
- [`parse_parts_lat()`](https://docs.ropensci.org/parzer/reference/parse_parts.md)
  and
  [`parse_parts_lon()`](https://docs.ropensci.org/parzer/reference/parse_parts.md)
  (and
  [`pz_degree()`](https://docs.ropensci.org/parzer/reference/dms.md),
  [`pz_minute()`](https://docs.ropensci.org/parzer/reference/dms.md),
  [`pz_second()`](https://docs.ropensci.org/parzer/reference/dms.md)) no
  longer lose the sign of values between -1 and 0: the sign now sits on
  the first non-zero part, e.g. -0.5 gives deg 0, min -30. They also no
  longer return 60 seconds, e.g. 10.35 is now 10 deg 21 min 0 sec, not
  10 deg 20 min 60 sec.
- [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  now gives one clear warning (“could not split into latitude and
  longitude”) for each string it cannot split, and keeps the other rows.
  Before, each such string gave two confusing “no digits” warnings about
  an internal marker.
- The warning text for an invalid letter after degrees uses plain ASCII
  quotes.
- [`parse_lon_lat()`](https://docs.ropensci.org/parzer/reference/parse_lon_lat.md)
  and
  [`parse_hemisphere()`](https://docs.ropensci.org/parzer/reference/parse_hemisphere.md)
  now clean symbols like `°` the same way
  [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md)
  and
  [`parse_lon()`](https://docs.ropensci.org/parzer/reference/parse_lon.md)
  do (before, “40°25’” gave 40).
- Small numeric inputs such as `1e-5` are no longer misread as two
  slots, and a number too large for a double now gives `NA` with a
  warning instead of shifting the other slots.
- [`parse_lon_lat()`](https://docs.ropensci.org/parzer/reference/parse_lon_lat.md)
  and
  [`parse_hemisphere()`](https://docs.ropensci.org/parzer/reference/parse_hemisphere.md)
  now accept numeric and integer inputs, as documented.
- [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  now returns NA for unrecognized formats instead of two empty strings.
- Strings that cannot be converted (no digits, or more than 3 numeric
  slots) now give only one warning, with no spurious second range
  warning.
- Fixed undefined behaviour in
  [`parse_parts_lat()`](https://docs.ropensci.org/parzer/reference/parse_parts.md)
  and
  [`parse_parts_lon()`](https://docs.ropensci.org/parzer/reference/parse_parts.md)
  when a value could not be parsed (NA was cast to an integer).

#### MINOR IMPROVEMENTS

- Faster parsing: the C++ code no longer uses regular expressions
  (`std::regex`), numbers are read with `std::stod` instead of a new
  stream for each token, and `parse_parts_*()` no longer allocates an R
  list for each coordinate.
  [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md),
  [`parse_lon()`](https://docs.ropensci.org/parzer/reference/parse_lon.md),
  [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  and
  [`parse_parts_lat()`](https://docs.ropensci.org/parzer/reference/parse_parts.md)
  are about 9 to 12 times faster than in 0.4.4.
- withr is no longer a required dependency (it moved to Suggests). The
  Windows-only [`.Call()`](https://rdrr.io/r/base/CallExternal.html)
  wrapper that forced the C locale
  ([\#31](https://github.com/ropensci/parzer/issues/31)) is removed,
  because it only worked around a `std::regex` bug.
- [`parse_hemisphere()`](https://docs.ropensci.org/parzer/reference/parse_hemisphere.md)
  detects the sign of a value with `std::signbit()` instead of
  converting it to a string.
- Added performance tests (`bench`) to detect slowdowns, and a larger
  test suite (about 98% coverage).
- The use cases vignette now runs its sf and leaflet code only when both
  packages are installed.
- README: local coverage badge, and installation with
  [`pak::pkg_install()`](https://pak.r-lib.org/reference/pkg_install.html).

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
