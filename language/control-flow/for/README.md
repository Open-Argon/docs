# `for` loops and iterators

## Basic syntax

`for (item in iterable)` runs the body once for each value produced by `iterable`:

```argon
for (item in [1, 2, 3]) do
    term.log(item)

for (n in 1 to 3) term.log(n)
```

Anything iterable can be looped over:

| Value | Yields |
|---|---|
| array | each element |
| tuple | each element |
| string | each **byte**, as a one-byte string (see [Strings](../../strings/README.md#strings-are-utf-8-bytes)) |
| dictionary | a `tuple(key, value)` for each entry, in insertion order |
| range (`a to b`, `a until b`, `range(...)`) | each number |
| your own object | whatever its iterator returns (see below) |

## Destructuring the loop variable

The loop variable can be a [destructuring pattern](../../variables/README.md#destructuring):

```argon
for ([key, value] in {a: 1, b: 2}) term.log(key, value)

for ([x, y] in [[1, 2], [3, 4]]) term.log(x + y)

for ({name, age} in [{name: "Ada", age: 36}]) term.log(name, age)
```

## Ranges

`a to b` includes `b`; `a until b` stops before it:

```argon
term.log(array(1 to 5))      # [1, 2, 3, 4, 5]
term.log(array(1 until 5))   # [1, 2, 3, 4]
```

The built-in `range` type works like Python's `range`, taking `range(stop)`,
`range(start, stop)` or `range(start, stop, step)`. `stop` is excluded:

```argon
term.log(array(range(5)))           # [0, 1, 2, 3, 4]
term.log(array(range(0, 10, 2)))    # [0, 2, 4, 6, 8]
```

A range whose start is past its stop is empty: `5 to 1` produces nothing. To count down,
use `range` with a negative step: `range(5, 0, -1)`. Bounds and steps can be fractional:
`range(0, 1, 0.25)` gives `0, 0.25, 0.5, 0.75`.

Ranges produce their values lazily, so `for (i in 0 until 1e7)` doesn't build a
ten-million-element array.

### Membership

`value in range` checks whether looping over the range would produce `value`, without
looping:

```argon
let age = 30
term.log(age in 18 to 65)            # true
term.log(4 in range(0, 10, 2))       # true
term.log(5 in range(0, 10, 2))       # false: not a multiple of the step
term.log(2.5 in 0 to 5)              # false: only whole steps from the start
```

To check whether a number lies anywhere between two bounds, compare it instead:
`low <= x && x <= high`.

## The iterator protocol

Any object can be made iterable by implementing two methods:

- `__iter__(self)` returns an **iterator** object. It can return `self`.
- `__next__(self)` is called on the iterator for each step. It returns the next value, or
  throws `StopIteration()` when there are no more values.

```argon
class Countdown do
    this.__init__(self, start) = do
        self.current = start
    this.__iter__(self) = self
    this.__next__(self) = do
        if (self.current <= 0) throw StopIteration()
        let value = self.current
        self.current -= 1
        return value

for (n in Countdown(3)) term.log(n)   # 3, 2, 1
term.log(array(Countdown(3)))          # [3, 2, 1]
```

`StopIteration` is caught by the `for` loop itself, so you don't need to catch it. Anything
that accepts an iterable, such as `array(...)` and `tuple.of(...)`, uses the same protocol.

## Next

- [`break` and `continue`](../break-continue/README.md)
