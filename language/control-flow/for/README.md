# `for` loops and iterators

## Basic syntax

```
for (x in 0 until 1e7) do
    ...
```

(from [`range_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/range_test.ar))

`for (item in iterable) do ...` iterates over anything **iterable** — arrays, ranges,
strings, dictionaries, or any custom object that implements the iterator protocol
described below.

```
for (item in [1, 2, 3]) do
    term.log(item)
```

## Ranges

`a until b` produces a range you can loop over. The standard library also exposes a
`range(...)` function (used e.g. in
[`map_filter_sort.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/map_filter_sort.ar) as
`range(-1000000, 1000000)`), which is the built-in `range` type
(`ARGON_RANGE_ITERATOR_TYPE`) constructed directly.

## The iterator protocol

Any object can be made iterable by implementing two dunder methods:

- `__iter__(self)` — returns an **iterator** object.
- `__next__(self)` — called repeatedly on the iterator; each call returns the next value,
  or throws `StopIteration()` once exhausted.

Here's a full custom range implementation from the test suite:

```
class custom_range_iterator do
    this.__init__(self, stop) = do
        self.current = 0
        self.stop = stop
    this.__next__(self) = do
        if (self.stop <= self.current) do
            throw StopIteration()
        let result = self.current
        self.current = self.current + 1
        return result

class custom_range do
    this.__init__(self, stop) = do
        self.stop = stop
    this.__iter__(self) = custom_range_iterator(self.stop)

for (x in custom_range(1e4)) do
    for (y in custom_range(1e3)) do
        ...
```

(from [`custom_range.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/custom_range.ar))

`StopIteration` is one of the built-in system exceptions (see
[Errors and exceptions](../../error-handling/README.md)) and is how iterators signal "no
more values" — it's caught internally by the `for` loop machinery, not something you
usually need to catch yourself.

## Nested loops

Loops can be nested arbitrarily, as shown in the `custom_range` example above.

## Next

- [`break` and `continue`](../break-continue/README.md)