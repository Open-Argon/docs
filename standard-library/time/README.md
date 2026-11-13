# `time`

```argon
import "time" as time
```

## `time.snooze(seconds)`

Pauses the current thread for `seconds`, which may be fractional:

```argon
import "time" as time

term.log("Starting")
time.snooze(0.5)   # half a second
term.log("Finished")
```

For the current date and time, or for measuring how long something takes, see the
[`date`](../date/README.md) module.

## Next

- [`regex`](../regex/README.md)
