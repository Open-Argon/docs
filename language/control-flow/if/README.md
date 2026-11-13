# `if` / `else`

```argon
let x = 5

if (x > 10) do
    term.log("big")
else if (x > 0) do
    term.log("small positive")
else do
    term.log("zero or negative")
```

`else` and `else if` must start a new line at the same indentation as the `if`.

## Single-statement form

If a branch is a single statement, `do` can be omitted:

```argon
let factorial(x) = do
    if (x <= 1) return 1
    return x * factorial(x - 1)

let n = 7
if (n % 2 == 0) term.log("even")
else term.log("odd")

term.log(factorial(5))   # 120
```

## Truthiness

Any value can be used as a condition. `false`, `null`, `0` and `""` are falsy; everything
else is truthy, **including empty arrays and dictionaries**. See
[Variables: truthiness](../../variables/README.md#truthiness).

```argon
let items = []
if (items.length) term.log("has items")
else term.log("empty")
```

## Conditional expressions

`if` is a statement. For a conditional *value*, use the ternary operator:

```argon
let a = 3
let sign = a < 0 ? -1 : a > 0 ? 1 : 0
term.log(sign)   # 1
```

## Next

- [`while` loops](../while/README.md)
