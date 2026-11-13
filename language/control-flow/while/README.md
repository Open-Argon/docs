# `while` loops

```argon
let i = 0
while (i < 10) do
    term.log(i)
    i += 1
```

The body can be a single statement:

```argon
let n = 1
while (n < 1000) n *= 2
term.log(n)   # 1024
```

The condition is re-evaluated before each iteration and can be any expression, including
an assignment. This loop keeps going while the popped value is truthy:

```argon
let queue = ["a", "b", "c"]
let item
while (item = queue.length ? queue.pop(0) : null) do
    term.log("processing", item)
```

## Infinite loops

`forever` loops until it is stopped with `break` or `return` (see
[`break` and `continue`](../break-continue/README.md)). It's the same as `while (true)`:

```argon
let attempts = 0
forever do
    attempts += 1
    if (attempts >= 3) break
term.log(attempts)   # 3
```

## Next

- [`for` loops and iterators](../for/README.md)
