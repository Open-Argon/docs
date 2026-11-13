# `date`

```argon
import "date" as date
```

Dates, times and durations. A `Date` is a moment in time, stored as a Unix timestamp in
milliseconds. Its calendar fields (`year`, `month`, ...) are in **UTC**, and there are
matching functions for the computer's local time zone. A `Duration` is a length of time.

```argon
import "date" as date

let launch = date.Date(year = 2026, month = 7, day = 28, hour = 15)
let week_later = launch + date.Duration(weeks = 1)

term.log(launch)                         # 2026-07-28T15:00:00.000Z
term.log(week_later.to_string("%d %b"))  # 04 Aug
term.log(week_later - launch)            # 7 days, 00:00:00
term.log(launch.weekday_name)            # Tuesday
```

## Creating dates

### `Date(...)`

```
Date(timestamp_ms=null, timestamp_s=null, date=null,
     year=null, month=null, day=null, hour=null, minute=null, second=null, millisecond=null,
     utc=true)
```

Describe the date in **one** of these ways, by keyword:

| Arguments | Meaning |
|---|---|
| `timestamp_ms` | Unix timestamp in milliseconds |
| `timestamp_s` | Unix timestamp in seconds |
| `date` | a copy of another `Date` |
| `year`, `month`, `day`, `hour`, `minute`, `second`, `millisecond` | calendar fields; missing ones default to the start of 1970-01-01 |
| *(nothing)* | the Unix epoch, 1970-01-01T00:00:00Z |

Calendar fields are read as UTC, or as local time if `utc = false`. Fields that are out of
range roll over: `Date(year = 2026, month = 13)` is January 2027, and `second = 90` is a
minute and a half.

```argon
import "date" as date

term.log(date.Date(timestamp_ms = 0))                   # 1970-01-01T00:00:00.000Z
term.log(date.Date(timestamp_s = 1785250800))           # 2026-07-28T15:00:00.000Z
term.log(date.Date(year = 2026, month = 7, day = 28))   # 2026-07-28T00:00:00.000Z
term.log(date.Date(year = 2026, month = 7, day = 28, hour = 9, utc = false).to_local_string())   # 2026-07-28 09:00:00
```

### `Date.now()` and `Date.today(utc=true)`

`Date.now()` is the current moment. `Date.today()` is midnight at the start of today, in
UTC or (with `utc = false`) in local time.

### `Date.from_string(text, format="%Y-%m-%d %H:%M:%S", utc=true)`

Parses a date using a [format string](#format-strings). The result is read as UTC, or as
local time if `utc = false`. Fields missing from the format default to 1970-01-01 00:00:00.
If `text` doesn't match the format exactly, it throws a `ValueError`:

```argon
import "date" as date

let d = date.Date.from_string("28/07/2026 09:30", "%d/%m/%Y %H:%M")
term.log(d)   # 2026-07-28T09:30:00.000Z

try date.Date.from_string("next tuesday", "%d/%m/%Y")
catch (ValueError as e) term.log(e.message)
```

### `Date.from_iso_string(text, utc=true)`

Parses an ISO 8601 / RFC 3339 date, as used by JSON APIs. The time, seconds, fractional
seconds and time zone are optional. A time zone suffix (`Z`, `+01:00`, `-0530`) is always
respected; without one, the time is read as UTC (or local time if `utc = false`). Invalid
dates, like `2026-02-30`, throw a `ValueError`.

```argon
import "date" as date

term.log(date.Date.from_iso_string("2026-07-28"))                 # 2026-07-28T00:00:00.000Z
term.log(date.Date.from_iso_string("2026-07-28T15:30:00.25Z"))    # 2026-07-28T15:30:00.250Z
term.log(date.Date.from_iso_string("2026-07-28 15:30:00+01:00"))  # 2026-07-28T14:30:00.000Z
```

## Fields

These are read as attributes, and are all in UTC (see [Local time](#local-time) for local
fields):

| Field | Description |
|---|---|
| `year` | e.g. `2026` |
| `month` | `1` to `12` |
| `day` | `1` to `31` |
| `hour` | `0` to `23` |
| `minute` | `0` to `59` |
| `second` | `0` to `59` |
| `millisecond` | `0` to `999` |
| `day_of_week` | `0` (Sunday) to `6` (Saturday) |
| `day_of_year` | `1` to `366` |
| `weekday_name` | e.g. `"Tuesday"` |
| `month_name` | e.g. `"July"` |
| `is_leap_year` | whether the date's year is a leap year |
| `days_in_month` | the number of days in the date's month |

`year`, `month`, `day`, `hour`, `minute`, `second` and `millisecond` can be assigned (or set
with `set_year(value)`, `set_month(value)`, ...), which changes the date **in place**.
To get a changed copy instead, use `replace`:

```argon
import "date" as date

let d = date.Date(year = 2026, month = 1, day = 31)
let first = d.replace(day = 1)
d.month = 3
term.log(first, d)   # 2026-01-01T00:00:00.000Z 2026-03-31T00:00:00.000Z
```

`replace(year=null, month=null, day=null, hour=null, minute=null, second=null, millisecond=null, utc=true)`
works on the UTC fields, or the local fields if `utc = false`.

## Durations and date arithmetic

`Duration(weeks=0, days=0, hours=0, minutes=0, seconds=0, milliseconds=0)` is a length of
time. Durations can be negative and fractional.

| Expression | Result |
|---|---|
| `date + duration`, `date - duration` | a `Date` |
| `date - other_date` | a `Duration` |
| `duration + duration`, `duration - duration`, `-duration` | a `Duration` |
| `duration * number`, `duration / number` | a `Duration` |
| `duration / duration` | a number (how many times one fits into the other) |

Dates and durations can be compared with `==`, `!=`, `<`, `<=`, `>` and `>=`, and used as
dictionary keys.

```argon
import "date" as date

let start = date.Date(year = 2026, month = 7, day = 28, hour = 9)
let end = date.Date(year = 2026, month = 12, day = 25)
let gap = end - start

term.log(gap)                     # 149 days, 15:00:00
term.log(gap.total_days)          # 149.625
term.log(start < end)             # true
term.log(date.Duration(hours = 1) / date.Duration(minutes = 15))   # 4
```

A `Duration` has `total_milliseconds`, `total_seconds`, `total_minutes`, `total_hours` and
`total_days` properties. Printing it gives `[N days, ]HH:MM:SS[.mmm]`.

### Calendar arithmetic

| Method | Returns |
|---|---|
| `add(weeks=0, days=0, hours=0, minutes=0, seconds=0, milliseconds=0)` | the date plus that much time |
| `add_months(n, utc=true)` | the same day `n` months later (or earlier, if negative); the day is clamped to the month's length |
| `add_years(n, utc=true)` | the same date `n` years later; February 29 becomes February 28 in non-leap years |
| `start_of_day(utc=true)` / `end_of_day(utc=true)` | 00:00:00.000 / 23:59:59.999 on the same day |
| `start_of_month(utc=true)` / `start_of_year(utc=true)` | midnight on the first day of the month / year |
| `copy()` | a copy of the date |

With `utc = false`, these work on local calendar days and months.

```argon
import "date" as date

let d = date.Date(year = 2026, month = 1, day = 31, hour = 12)
term.log(d.add_months(1))          # 2026-02-28T12:00:00.000Z
term.log(d.add(days = 1, hours = 6))   # 2026-02-01T18:00:00.000Z
term.log(d.start_of_month())       # 2026-01-01T00:00:00.000Z
```

## Local time

| Method | Description |
|---|---|
| `local()` | the local calendar fields as a dictionary: `year`, `month`, `day`, `hour`, `minute`, `second`, `millisecond`, `day_of_week`, `day_of_year` and `utc_offset` |
| `utc_offset()` | the local time zone's offset from UTC at this moment, as a `Duration` (it includes daylight saving time) |
| `to_local_string(format="%Y-%m-%d %H:%M:%S")` | format in local time |
| `to_local_iso_string()` | ISO 8601 with the local offset, e.g. `2026-07-28T16:00:00.000+01:00` |

```argon
import "date" as date

let now = date.Date.now()
let here = now.local()
term.log(`It's $(here.hour):$(here.minute) here, UTC$(now.to_local_iso_string()[-6:])`)
```

## Formatting and timestamps

| Method | Returns |
|---|---|
| `to_string(format="%Y-%m-%d %H:%M:%S")` | the date formatted in UTC |
| `to_iso_string()` | ISO 8601 in UTC, e.g. `2026-07-28T15:00:00.000Z` (also what printing a date shows) |
| `http_date()` | HTTP date format, e.g. `Tue, 28 Jul 2026 15:00:00 GMT` |
| `unix()` | Unix timestamp in whole seconds |
| `unix_ms()` | Unix timestamp in milliseconds |

## Measuring elapsed time

`Date.monotonic()` returns a reading, in nanoseconds, from a clock that never jumps when the
system clock changes. Subtract two readings to time something:

```argon
import "date" as date
import "time" as time

let start = date.Date.monotonic()
time.snooze(0.1)
let elapsed_ms = (date.Date.monotonic() - start) / 1000000
term.log(elapsed_ms >= 100)   # true
```

## Module functions

| Name | Description |
|---|---|
| `date.is_leap_year(year)` | whether `year` is a leap year |
| `date.days_in_month(year, month)` | the number of days in a month |
| `date.WEEKDAYS` | `["Sunday", ..., "Saturday"]` |
| `date.MONTHS` | `["January", ..., "December"]` |

## Format strings

Formatting and parsing use `strftime`-style codes. Some common ones:

| Code | Meaning | Example |
|---|---|---|
| `%Y` | four-digit year | `2026` |
| `%m` | month number | `07` |
| `%d` | day of the month | `28` |
| `%H` | hour (24-hour clock) | `15` |
| `%M` | minute | `30` |
| `%S` | second | `00` |
| `%a` / `%A` | weekday name | `Tue` / `Tuesday` |
| `%b` / `%B` | month name | `Jul` / `July` |
| `%Z` | time zone name (in `to_local_string`) | `BST` |
| `%%` | a literal `%` | `%` |

Names produced by `%a`, `%b` and so on depend on the system's locale. The `weekday_name` and
`month_name` fields are always English.

## Next

- [`time`](../time/README.md)
