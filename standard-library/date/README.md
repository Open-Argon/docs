# `date`

```
import "date" as date
```

Provides date and time handling using Unix timestamps internally. Dates are represented with millisecond precision and support creation, formatting, parsing, and accessing or modifying date components.

## `Date(timestamp_ms=null, timestamp_s=null, date=null, year=null, month=null, ...)`

Creates a new date object.

Dates can be created from timestamps:

```
let a = date.Date(timestamp_ms=0)
term.log(a.to_string())   # "1970-01-01 00:00:00"

let b = date.Date(timestamp_s=1)
term.log(b.to_string())   # "1970-01-01 00:00:01"
```

A date can also be copied from another `Date` object:

```
let a = date.Date(timestamp_s=100)
let b = date.Date(date=a)

term.log(b.unix())   # 100
```

Individual date components can be provided when constructing a date. Missing components use the default values:

```
let d = date.Date(year=2026, month=7, day=28)

term.log(d.to_string())   # "2026-07-28 00:00:00"
```

## `Date.now()`

Returns a `Date` representing the current time.

```
let now = date.Date.now()

term.log(now.to_string())
```

## `Date.now_utc()`

Returns the current UTC time.

Currently this is equivalent to `Date.now()` because dates are stored internally as UTC timestamps.

## `Date.from_string(str, fmt="%Y-%m-%d %H:%M:%S")`

Creates a `Date` by parsing a formatted date string.

```
let d = date.Date.from_string("2026-07-28 12:30:00")

term.log(d.year)    # 2026
term.log(d.month)   # 7
term.log(d.day)     # 28
```

The format string uses `strftime`-style format specifiers:

```
let d = date.Date.from_string("28/07/2026", "%d/%m/%Y")

term.log(d.to_string())   # "2026-07-28 00:00:00"
```

## `date.unix()`

Returns the date as a Unix timestamp in seconds.

```
let d = date.Date(timestamp_s=1000)

term.log(d.unix())   # 1000
```

## `date.unix_ms()`

Returns the date as a Unix timestamp in milliseconds.

```
let d = date.Date(timestamp_ms=1000)

term.log(d.unix_ms())   # 1000
```

## Date properties

The following properties are available:

- `year`
- `month`
- `day`
- `hour`
- `minute`
- `second`
- `day_of_week`
- `day_of_year`

Example:

```
let d = date.Date(year=2026, month=7, day=28, hour=15)

term.log(d.year)    # 2026
term.log(d.month)   # 7
term.log(d.day)     # 28
term.log(d.hour)    # 15
```

`day_of_week` returns the weekday number, and `day_of_year` returns the day number within the year.

## `date.to_string(fmt="%Y-%m-%d %H:%M:%S")`

Formats the date as a UTC string.

```
let d = date.Date(year=2026, month=7, day=28)

term.log(d.to_string())
# "2026-07-28 00:00:00"

term.log(d.to_string("%d/%m/%Y"))
# "28/07/2026"
```

## `date.to_local_string(fmt="%Y-%m-%d %H:%M:%S")`

Formats the date using the local timezone.

```
let d = date.Date.now()

term.log(d.to_local_string())
```

## `date.http_date()`

Formats the date using the HTTP-date format.

```
let d = date.Date(timestamp_s=0)

term.log(d.http_date())
# "Thu, 01 Jan 1970 00:00:00 GMT"
```

## Date modification

The date components can be changed using setters:

- `set_year(value)`
- `set_month(value)`
- `set_day(value)`
- `set_hour(value)`
- `set_minute(value)`
- `set_second(value)`

Example:

```
let d = date.Date(year=2026, month=1, day=1)

d.set_month(7)
d.set_day(28)

term.log(d.to_string())
# "2026-07-28 00:00:00"
```

## `Date.monotonic()`

Returns the current monotonic clock value.

Unlike normal timestamps, monotonic time is not affected by system clock changes and is intended for measuring durations.

```
let start = date.Date.monotonic()

# do some work

let elapsed = date.Date.monotonic() - start
```

## Format strings

Formatting and parsing functions use `strftime`-style format codes.

Common format codes:

| Code | Meaning | Example |
| --- | --- | --- |
| `%Y` | Four digit year | `2026` |
| `%m` | Month number | `07` |
| `%d` | Day of month | `28` |
| `%H` | Hour (24-hour clock) | `15` |
| `%M` | Minute | `30` |
| `%S` | Second | `00` |
| `%a` | Short weekday name | `Tue` |
| `%b` | Short month name | `Jul` |

## Timezones

`Date` objects store timestamps as UTC internally.

Functions ending in `_utc` operate directly on UTC values, while local formatting functions apply the system timezone.

## Next

- [`json`](../json/README.md)
- [`file`](../file/README.md)