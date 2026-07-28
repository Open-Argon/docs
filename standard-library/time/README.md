# `time`

```
import "time" as time
```

Provides functions for working with delays and timing.

## `time.snooze(seconds)`

Pauses execution for the specified number of seconds.

`seconds` may be an integer or a rational number.

```
time.snooze(1)
```

Sleeps for one second before continuing execution.

Fractional seconds can be used for shorter delays:

```
time.snooze(0.5)
```

Sleeps for half a second.

Example:

```
term.log("Starting")

time.snooze(2)

term.log("Finished")
```

Output:

```
Starting
# waits 2 seconds
Finished
```

## Next

- [`date`](../date/README.md)
- [`json`](../json/README.md)