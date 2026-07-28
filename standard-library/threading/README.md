# `threading`

```
import "threading" as threading
```

Provides support for running code in separate threads and storing thread-local data.

## `threading.Thread(target)`

Creates a new thread that executes the given function.

The target function is called with no arguments when the thread starts.

```
let thread = threading.Thread(()=do
    term.log("Hello from a thread")
)

thread.start()
```

The target function's return value can be retrieved using `join()`.

## `Thread.start()`

Starts the thread.

```
let thread = threading.Thread(()=do
    term.log("Running")
)

thread.start()
```

Returns the `Thread` object, allowing threads to be created and started inline:

```
let thread = threading.Thread(()=do
    do_work()
).start()
```

Calling `start()` on an already started thread has no effect.

## `Thread.join()`

Waits for the thread to finish and returns the value returned by the target function.

```
let thread = threading.Thread(()=do
    return 42
)

thread.start()

let result = thread.join()

term.log(result)   # 42
```

If the thread has not been started, `join()` immediately returns the current return value.

## `Thread.detach()`

Detaches the thread, allowing it to continue running independently.

```
let thread = threading.Thread(()=do
    do_work()
)

thread.start()
thread.detach()
```

A detached thread cannot be joined later.

## `threading.get_id()`

Returns the ID of the current thread.

```
let id = threading.get_id()

term.log(id)
```

## `threading.Local()`

Creates thread-local storage.

Each thread has its own separate storage. Values assigned in one thread are not visible in other threads.

```
let local = threading.Local()

let worker = ()=do
    local.value = 10
    term.log(local.value)

let thread = threading.Thread(worker)

thread.start()
thread.join()

term.log(local.value)
```

Thread-local values can be accessed using attributes or indexing:

```
let local = threading.Local()

local.name = "main"
term.log(local.name)

local["count"] = 5
term.log(local["count"])
```

Each thread maintains its own values internally.

## Example: running multiple workers

```
let storage = threading.Local()

let worker = (value)=do
    storage.value = value

    let output = 1000000
    for (i in 0 until output) do
    return output

let threads = []

for (i in 0 until 4) do
    threads.append(threading.Thread(()=worker(i)).start())

for (thread in threads) do
    term.log(thread.join())
```

Each worker has its own `storage.value`, even though all workers share the same `Local` object.

## Next

- [`network`](../network/README.md)
- [`time`](../time/README.md)