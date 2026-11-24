# Parse longitude values

Parse longitude values

## Usage

``` r
parse_lon(lon, format = NULL)
```

## Arguments

- lon:

  (numeric/integer/character) one or more longitude values.

- format:

  (character) `NULL` (the default) or one or more patterns that say how
  the values are written, for example `"DDMMSSH"`. With `NULL`, parzer
  finds the degrees, minutes and seconds by itself. Give a pattern when
  it cannot, for example when the digits are packed together with no
  separator, as in `"512839N"`. With several patterns, each value is
  read with the first pattern that matches it. See the Format and Format
  examples sections.

## Value

numeric vector

## Format examples

The rules for `format` are in the Format section below.

|  |  |  |  |
|----|----|----|----|
| Value | `format` | Result | Why |
| `"0002741W"` | `"DDDMMSSH"` | -0.46139 | ICAO longitude |
| `"1452741E"` | `"DDDMMSSH"` | 145.46139 | ICAO longitude, east |
| `"01045.5W"` | `"DDDMM.mH"` | -10.75833 | NMEA |
| `"01045.12345W"` | `"DDDMM.mH"` | -10.75206 | after a dot, any number of decimals |
| `"W0104512345"` | `"HDDDMMmmmmm"` | -10.75206 | decimal point not written: exactly 5 decimals |
| `"E0451530"` | `"HDDDMMSS"` | 45.25833 | `H` in front |
| `"0451530e"` | `"DDDMMSSH"` | 45.25833 | `H` at the end, in lower case |
| `"045E15.5"` | `"DDDHMM.m"` | 45.25833 | `H` in the middle |
| `"07443 56W"` | `"DDDMM SSH"` | -74.73222 |  |
| `"074 3056W"` | `"DDD MMSSH"` | -74.51556 |  |
| `"074 3056W"` | `"DDD MMSSH"` | -74.51556 | a space matches several spaces |
| `"074°30'56\"W"` | `"DDD°MM'SS\"H"` | -74.51556 | symbols must match exactly |
| `"074:30:56"` | `"DDD:MM:SS"` | 74.51556 |  |
| `"120.5"` | `"DDD.d"` | 120.5 | decimal degrees |
| `"-0743056"` | `"DDDMMSS"` | -74.51556 | minus sign |
| `"\u22120743056"` | `"DDDMMSS"` | -74.51556 | Unicode minus sign |
| `"-074W"` | `"DDDH"` | -74 | sign and letter agree |
| `"+074W"` | `"DDDH"` | -74 | the letter gives the sign |
| `"-074E"` | `"DDDH"` | `NA` | sign and letter disagree |
| `"074N"` | `"DDDH"` | `NA` | `N` is not a longitude letter |
| `"0743056W"` | `c("DDDMMSSH", "DDD MM SSH")` | -74.51556 | the first pattern matches |
| `"074 30 56W"` | `c("DDDMMSSH", "DDD MM SSH")` | -74.51556 | the second pattern matches |
| `"74 30 56W"` | `c("DDD MM SSH", "DD MM SSH", "D MM SSH")` | -74.51556 | no leading zero: the second pattern matches |
| `"7 30 56W"` | `c("DDD MM SSH", "DD MM SSH", "D MM SSH")` | -7.51556 | no leading zeros: the third pattern matches |
| `"74 30 56W"` | `"DDD MM SSH"` | `NA` | `DDD` needs exactly 3 digits |
| `"0747530W"` | `"DDDMMSSH"` | `NA` | 75 minutes |
| `"1800000W"` | `"DDDMMSSH"` | -180 | lowest longitude |
| `"1800001W"` | `"DDDMMSSH"` | `NA` | longitude below -180 |
| `"18030W"` | `"DDDMMH"` | `NA` | longitude below -180 |
| `"3600000E"` | `"DDDMMSSH"` | 360 | highest longitude |
| `"3600001E"` | `"DDDMMSSH"` | `NA` | longitude above 360 |

## Errors

Throws warnings on parsing errors, and returns `NA` in each case

Types of errors:

- invalid argument: e.g., letters passed instead of numbers, see
  <https://en.cppreference.com/cpp/error/invalid_argument>

- out of range: numbers of out acceptable range, see
  <https://en.cppreference.com/cpp/error/out_of_range>

- out of longitude range: not within -180/360 range

- minutes or seconds out of range (negative or 60 and above): not within
  0/60 range, e.g. `"10 30,12345"` is read as 10 degrees, 30 minutes and
  12345 seconds, so it gives a warning and `NA`

- a decimal degree or minute followed by another slot: only the last
  slot can be decimal, e.g. `"45.5 30"`

- a minus sign that disagrees with the direction letter: minus sign and
  direction letter disagree, e.g. `"E -45"` or `"-45E"`

- a number with two decimal points: invalid number, e.g. `"40.25.30"`

- with `format`, a value that does not match any pattern: does not match
  format, see the Format section

A comma or space is always a separator between degrees, minutes and
seconds, never a decimal mark.

## Format

A pattern describes how the values are written. It is made of letters
and other characters. The idea comes from `AngleFormat` in Apache SIS
and from `CLongLatString`.

- `D`, `M` and `S` stand for the digits of the whole degrees, minutes
  and seconds. The number of letters is the exact number of digits: `DD`
  means exactly 2 digits, so it reads `"05"` but not `"5"`, and `DDD`
  means exactly 3 digits, as longitudes often need. Values written
  without leading zeros need a second pattern, for example
  `c("DD MM SSH", "D MM SSH")` reads both `"45 03 02N"` and
  `"5 03 02N"`. `D` is required. `M` and `S` are optional, but `S` needs
  `M`, and they come in the order `D`, `M`, `S`.

- `d`, `m` and `s` stand for the decimal digits of the degrees, minutes
  or seconds. Only the last unit can have them. They come right after
  it, as in `"DDMMmmm"`, or after a dot that follows it, as in
  `"DDMM.m"`.

  - Without a dot, the decimal point is not written in the value. The
    number of letters is then the exact number of decimal digits: it is
    the only way to know where the hidden decimal point is.
    `"HDDMMmmmmm"` reads `"N455698735"` as N 45 degrees 56.98735
    minutes.

  - After a dot, the value has a decimal point and one or more decimal
    digits, as many as you like: `"DDMM.m"` reads both `"4556.9"` and
    `"4556.98735"`.

- `H` stands for the hemisphere letter: `N` or `S` in
  [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md),
  `E` or `W` in `parse_lon()`, in upper or lower case. `S` and `W` give
  a negative value. `H` can be at the start, at the end or in the
  middle, but only once. A letter of the other pair, for example `E` in
  [`parse_lat()`](https://docs.ropensci.org/parzer/reference/parse_lat.md),
  gives `NA`.

- A space matches one or more spaces (or other white space).

- Any other character, for example `°`, `'`, `"`, `:`, or a dot that is
  not followed by `d`, `m` or `s`, must be in the value exactly as in
  the pattern.

- A sign can always be written right before the degrees: `+`, `-`, or a
  Unicode minus (U+2212), en dash `–` or em dash `—`. With `H`, the
  rules are the same as with `format = NULL`: `"-45S"` and `"+45S"` give
  -45, but `"-45N"` gives `NA`, because the minus sign and the letter
  disagree.

Spaces at the start and at the end of a value are ignored. The pattern
is matched against the value as it is given: the cleaning done with
`format = NULL`, for example reading `d` or `º` as a degree mark, does
not happen.

A value gives `NA` when it does not match any pattern, when its minutes
or seconds are 60 or more, or when the result is out of range (latitude
outside -90/90, longitude outside -180/360). All these values are listed
in one warning. They are not read again with `format = NULL`. A missing
value (`NA`) or an empty string gives `NA` without a warning. A pattern
that cannot work, for example `"MMDD"`, `"DDmmSS"` or `"DDX"`, is an
error.

Numbers are turned into text first, so they lose their leading zeros:
`parse_lon(0002741, format = "DDDMMSS")` sees `"2741"` and gives `NA`.
Give such values as text: `parse_lon("0002741", format = "DDDMMSS")`.

See the Format examples section for many examples.

## Examples

``` r
parse_lon("")
#> [1] NA

# with format: say how the values are written (see the Format section)
parse_lon("0002741W", format = "DDDMMSSH") # ICAO => -0.46139
#> [1] -0.4613889
parse_lon("E0451530", format = "HDDDMMSS") # => 45.25833
#> [1] 45.25833
parse_lon("01045.5W", format = "DDDMM.mH") # NMEA => -10.75833
#> [1] -10.75833
parse_lon(c("0002741W", "000 27 41W"), format = c("DDDMMSSH", "DDD MM SSH"))
#> [1] -0.4613889 -0.4613889
# no leading zeros: one format for each number of degree digits
parse_lon(
  c("074 30 56W", "74 30 56W", "7 30 56W"),
  format = c("DDD MM SSH", "DD MM SSH", "D MM SSH")
)
#> [1] -74.515556 -74.515556  -7.515556
# NA and one warning: N is not a longitude letter, below -180
parse_lon(c("045N", "18030W"), format = c("DDDH", "DDDMMH"))
#> Warning: NA for 2 value(s) that could not be read with `format`:
#>   "045N": direction letter must be E or W
#>   "18030W": not within -180/360 range
#> [1] NA NA

if (FALSE) { # \dontrun{
parse_lon("-181")
parse_lon("-361")
parse_lon("95")
parse_lon("asdfaf")

parse_lon("45")
parse_lon("-45")
parse_lon("-45.2323")
parse_lon("334")

# out of range with std::stod?
parse_lon("-45.23232e24")
parse_lon("-45.23232e2")
parse_lon("-45.23232")

# numeric input
parse_lon(1:10)
parse_lon(85:94)

# different formats
parse_lon("40.4183318 E")
parse_lon("40.4183318 W")
parse_lon("40 25 5.994") # => 40.41833

parse_lon("40.4183318W")
parse_lon("W40.4183318")
parse_lon("E40.4183318")
parse_lon("40.4183318E")

parse_lon("E 39 21.440") # => 39.35733
parse_lon("W 56 1.389") # => -56.02315

parse_lon("E40°25’5.994") # => 40.41833
parse_lon("40° 25´ 5.994\" E") # => 40.41833
parse_lon("40:25:6E")
parse_lon("40:25:5.994E")
parse_lon("40d 25’ 6\" E")
} # }
```
