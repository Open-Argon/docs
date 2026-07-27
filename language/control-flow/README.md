# Control flow

Argon has the control flow constructs you'd expect from a C-family / scripting language:

- [`if` / `else`](if/README.md)
- [`while` loops](while/README.md)
- [`for` loops and iterators](for/README.md)
- [`break` and `continue`](break-continue/README.md)

All of these use `do` blocks instead of curly braces. A condition is always wrapped in
parentheses:

```
if (x > 0) do
    term.log("positive")
```

A single-statement body doesn't strictly need `do` in every case (see
[`factorial.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/factorial.ar), where `if (n) return x*factorial(n)`
has no `do`), but using `do` for multi-statement bodies is required.