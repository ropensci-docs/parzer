# Changelog

## parzer 0.6.0

CRAN release: 2026-11-24

#### NEW FEATURES

- [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md)
  and
  [`parse_lon()`](https://docs.ropensci.org/parzer/reference/parse_lon.md)
  have a working `format` argument
  ([\#7](https://github.com/ropensci/parzer/issues/7),
  [\#11](https://github.com/ropensci/parzer/issues/11)). A pattern such
  as `"DDMMSSH"` says how the values are written, so packed digits can
  be read: ICAO `"512839N"`, NMEA `"4556.98735"` with `"DDMM.mmmmm"`, or
  `"N455698735"` with `"HDDMMmmmmm"` (decimal point not written).
  Several patterns can be given; each value is read with the first one
  that matches. Values that do not match, or are out of range, give `NA`
  and are listed in one warning. See the new Format section of
  [`?parse_lat`](https://docs.ropensci.org/parzer/reference/parse_lat.md).
- [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  has a `format` argument
  ([\#11](https://github.com/ropensci/parzer/issues/11)). One pattern
  describes the whole string, for example `"DDMMSSH DDDMMSSH"` for an
  ICAO pair such as “512839N 0002741W”. The two coordinates need no
  separator: `"DDMMSSHDDDMMSSH"` reads “512839N0002741W”, and
  `"HD.ddddHDDD.ddd"` reads “N4.9196E101.347”, which could not be read
  before. Several patterns can be given; each string is read with the
  first one that matches. Strings that do not match, or with a value out
  of range, are listed in one warning. See the Format section of
  [`?parse_llstr`](https://docs.ropensci.org/parzer/reference/parse_llstr.md).
- [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  has an `order` argument: `"auto"` (the default), `"lat_lon"` or
  `"lon_lat"`. With `"auto"`, latitude comes first, unless the direction
  letters only fit longitude first: “W74.5, N45.5” and “74.5W, 45.5” now
  give lat 45.5 and lon -74.5. Letters that contradict each other (“E45,
  E30”), or that do not fit a forced order, give `NA` for both values
  with a warning. Only rows that gave at least one `NA` before can
  change.
- [`parse_lon_lat()`](https://docs.ropensci.org/parzer/reference/parse_lon_lat.md)
  has `lon_format` and `lat_format` arguments, passed to the `format`
  argument of
  [`parse_lon()`](https://docs.ropensci.org/parzer/reference/parse_lon.md)
  and
  [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md).
  The longitude comes first, as in the other arguments.

#### BUG FIX

- [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  no longer returns a latitude as the longitude when the values seem to
  be swapped. When the first value is out of the latitude range but is a
  valid longitude, and the string has no direction letter, as in
  “101.347, 4.9196”, both values are now `NA`, with a warning that
  suggests `order = "lon_lat"`. Before, this gave lat `NA` and lon
  4.9196. Other failures still keep the value that could be read: “abc,
  -74.5” gives lat `NA` and lon -74.5. Only these rows, which already
  had an `NA`, change.
- A Unicode minus sign (U+2212), an en dash or an em dash in front of a
  number is now read as a minus sign. Before, “−45.5” gave 45.5 without
  a warning.
- [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  cuts at the only space whatever the number of decimal points: “45
  74.5” gave lat `NA` and lon 5. A single decimal number such as “45.74”
  is no longer cut at the dot into two coordinates; it gives `NA` with a
  warning. The accepted formats are now listed in the documentation.
- A number with two decimal points, e.g. “40.25.30”, gives `NA` with a
  warning instead of silently reading 40.25.
- `NA` input gives `NA` without a warning in all functions. Before,
  every `NA` gave a “no digits detected, got: na” warning.
- A minus sign that disagrees with the hemisphere letter, e.g. “N -45”
  or “-45N”, gives `NA` with a warning. Before, “N -45” gave 45 and
  “-45N” gave -45, both silently. “S -45” still gives -45.
- The degree marks `d` and `g` are accepted in upper case too: “40G 25
  6N” gave `NA`.
- `pz_d(1) + 5` and the like now fail with a clear message (“both sides
  must be pz objects”); unary minus works and keeps the unit:
  `-pz_m(30)`.
- [`pz_degree()`](https://docs.ropensci.org/parzer/reference/dms.md),
  [`parse_lon_lat()`](https://docs.ropensci.org/parzer/reference/parse_lon_lat.md)
  and
  [`parse_hemisphere()`](https://docs.ropensci.org/parzer/reference/parse_hemisphere.md)
  give plain error messages for a missing argument or a length mismatch.
- The lifecycle badge figure is now shipped with the package, so the
  help page no longer shows a broken image.
- Documentation: invalid values give `NA`, not `NaN`.

#### MINOR IMPROVEMENTS

- When a value with 4 or more digits in a row (not after a decimal
  point), such as “2343 56N”, gives `NA` because its degrees, minutes or
  seconds are out of range, the warning now says that the digits may be
  packed together and points to the `format` argument. Before, a
  latitude warning said to check that lon and lat were not inverted.
- Faster input cleaning: it now runs in C++, without regular
  expressions, and turns numbers into text in one step instead of one by
  one.
  [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  also cleans each string once instead of three times. Measured against
  0.5.0 on 2026-11-23 on an installed package (macOS arm64, mixed
  inputs, median times):
  [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md)
  and
  [`parse_lon()`](https://docs.ropensci.org/parzer/reference/parse_lon.md)
  take 48 ms instead of 130 ms for 100 000 strings (2.7 times faster),
  and 70 to 75 ms instead of 1.1 s for 100 000 numbers (about 15 times
  faster).
  [`parse_parts_lat()`](https://docs.ropensci.org/parzer/reference/parse_parts.md)
  takes 4.9 ms instead of 13 ms for 10 000 strings, and
  [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  30 ms instead of 70 ms for 20 000 strings.
- Strings that are not text give an error, with or without `format`, as
  invalid UTF-8 already did: strings marked as `"bytes"` (see
  [`?Encoding`](https://rdrr.io/r/base/Encoding.html)), and strings
  marked as latin1 with one of the 5 bytes that Windows-1252 leaves
  undefined (0x81, 0x8D, 0x8F, 0x90, 0x9D). Before, a `"bytes"` string
  was cleaned byte by byte. Convert such strings to UTF-8 first, for
  example with [`iconv()`](https://rdrr.io/r/base/iconv.html).
- The performance tests now also catch moderate slowdowns, through a
  relative speed test: the time of
  [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md),
  [`parse_lon()`](https://docs.ropensci.org/parzer/reference/parse_lon.md)
  and
  [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md)
  on 10 000 strings is divided by the time of a base R
  [`gsub()`](https://rdrr.io/r/base/grep.html) on the same strings and
  must stay below a limit set from the ratios on 5 CI platforms: 6 for
  [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md)
  and
  [`parse_lon()`](https://docs.ropensci.org/parzer/reference/parse_lon.md),
  8 for
  [`parse_llstr()`](https://docs.ropensci.org/parzer/reference/parse_llstr.md).
  The older tests only caught large slowdowns, such as run time growing
  with the square of the input. The new test runs only on an installed
  package, not with `devtools::test()`.
- Removed a check in the C++ code that could never fire, and the
  Japanese-locale test for the old `std::regex` bug. callr, pkgbuild,
  pkgload and withr are no longer suggested.

## parzer 0.5.0

CRAN release: 2026-11-06

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
