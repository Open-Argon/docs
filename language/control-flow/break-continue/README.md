# `break` and `continue`

`break` exits the nearest enclosing loop; `continue` skips to the next iteration. Both
work in `while` and `for` loops.

```argon
for (i in 1 to 100) do
    if (i > 6) break
    if (i % 2 == 0) continue
    term.log(i)
```

This prints `1`, `3` and `5`. Even numbers are skipped by `continue`, and the loop stops
entirely once `i` reaches `7`.

With nested loops, `break` and `continue` only affect the innermost loop:

```argon
for (x in 1 to 3) do
    for (y in 1 to 3) do
        if (y > x) break
        term.log(x, y)
```

## Next

- [Functions](../../functions/README.md)
