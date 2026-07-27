# `if` / `else`

```
let x = 5

if (x > 10) do
    term.log("big")
else if (x > 0) do
    term.log("small positive")
else do
    term.log("zero or negative")
```

## Single-statement form

If the body is a single statement, `do` can be omitted:

```
let factorial(x) = do
    let n = x - 1
    if (n) return x*factorial(n)
    return 1
```

(from [`factorial.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/factorial.ar))

## Truthiness

Any value can be used as a condition. `0`, `null`, `false`, and empty strings/collections
are falsy; everything else is truthy. In the `factorial.ar` example above, `if (n)` is
shorthand for "if `n` is not zero".

## Ternary expression

For simple conditional *expressions* (not statements), see the ternary operator described
in [Operators](../../operators/README.md#ternary-conditional-expression):

```
let sign = a < b ? -1 : a > b ? 1 : 0
```

## Next

- [`while` loops](../while/README.md)