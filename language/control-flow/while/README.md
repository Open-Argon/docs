# `while` loops

```
let i = 0
while (i < 10) do
    term.log(i)
    i += 1
```

A `while` loop with a truthy constant condition (e.g. a non-zero length) can be used to
drain a collection:

```
import "../stdlib/random" expose random

let x = []
let i = 0

while (i < 1e6) do
    x.append(i)
    i = i + 1

let y = []
while (x.length) do             # loops while x.length is truthy (non-zero)
    let result = x.pop(random.int(0, x.length - 1))
    y.append(result)
```

(from [`array_pop.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array_pop.ar))

## `while` as an expression-friendly loop

`while` conditions are parenthesized expressions, so they can contain assignments,
function calls, comparisons, etc. — anything that evaluates to a value.

## Infinite loops

There's no dedicated `loop` keyword; use `while (true) do ...` and `break` to exit (see
[`break` and `continue`](../break-continue/README.md)). The test suite has a file named
[`foreverloop.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/foreverloop.ar) demonstrating this pattern.

## Next

- [`for` loops and iterators](../for/README.md)