# Control flow

Argon has the control flow constructs you'd expect from a scripting language:

- [`if` / `else`](if/README.md)
- [`while` and `forever` loops](while/README.md)
- [`for` loops and iterators](for/README.md)
- [`break` and `continue`](break-continue/README.md)
- [`try` / `catch`](../error-handling/README.md)

A condition is always wrapped in parentheses, and the body is either a `do` block or a
single statement:

```argon
let x = 5

if (x > 0) do
    term.log("positive")
    term.log("still positive")

if (x > 0) term.log("positive")
```

See [Syntax basics](../syntax/README.md#blocks-do) for how `do` blocks and indentation
work.

`if`, `while`, `for` and `try` are statements, not expressions: they can't be used as a
value (e.g. `let y = if (x) 1` is a syntax error). Use the
[ternary operator](../operators/README.md#conditional-ternary-expression) or a `do` block
for conditional values.

## Next

- [`if` / `else`](if/README.md)
