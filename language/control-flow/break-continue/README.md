# `break` and `continue`

`break` exits the nearest enclosing loop; `continue` skips to the next iteration.

```
let f() = do
    let x = 10
    let i = 0
    while (i < 100) do
        i = i + 1
        if (i > (x * 2)) break
        if (i > x) continue
        term.log(i)

    term.log(i, x)

let g() = f()

g()
```

(from
[`loop_continue_and_break_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/loop_continue_and_break_test.ar))

This example logs `1` through `10` (stopping the `term.log(i)` calls once `i > x`, via
`continue`), then keeps looping silently until `i` exceeds `x * 2`, at which point `break`
exits the loop entirely and the final `term.log(i, x)` runs — printing `21 10`.

Both keywords work inside `for` loops too, in exactly the same way.

## Next

- [Functions](../../functions/README.md)