# `threading`

```argon
import "threading" as threading
```

Runs functions on separate operating system threads.

## `threading.Thread(target)`

Creates a thread that will call `target()` (with no arguments) when started.

| Method | Description |
|---|---|
| `start()` | start the thread; returns the `Thread`, so it can be chained |
| `join()` | wait for the thread to finish and return `target`'s return value |
| `detach()` | let the thread run independently; it can't be joined afterwards |

```argon
import "threading" as threading

let worker = threading.Thread(() = do
    let total = 0
    for (i in 1 to 100) total += i
    return total
).start()

term.log(worker.join())   # 5050
```

If `target` throws, the exception is rethrown by `join()`:

```argon
import "threading" as threading

let t = threading.Thread(() = do
    throw ValueError("failed in thread")
).start()

try t.join()
catch (ValueError as e) term.log("caught:", e.message)
```

Calling `start()` on a thread that has already been started does nothing (and returns
`null`). Calling `join()` on a thread that was never started returns `null` immediately.

To pass arguments, capture them in a closure:

```argon
import "threading" as threading

let square(n) = n * n
let threads = []
for (i in 1 to 4) do
    let n = i
    threads.append(threading.Thread(() = square(n)).start())

term.log(threads.map((t) = t.join()))   # [1, 4, 9, 16]
```

## `threading.get_id()`

Returns a number identifying the current thread.

## `threading.Local()`

Thread-local storage: an object whose attributes (or items) have a separate value in each
thread.

```argon
import "threading" as threading

let local = threading.Local()
local.name = "main"

threading.Thread(() = do
    local.name = "worker"
    term.log("in thread:", local.name)   # worker
).start().join()

term.log("in main:", local.name)          # main
```

Values can be accessed as attributes (`local.name`) or items (`local["name"]`).

## Next

- [`subprocess`](../subprocess/README.md)
