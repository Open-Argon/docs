# Your first program

Create a file called `hello.ar`:

```
term.log("Hello, world!")
```

Run it with the `argon` interpreter:

```bash
argon hello.ar
```

You should see:

```
Hello, world!
```

## A quick tour

Argon files use the `.ar` extension. Comments start with `#`:

```
# this is a comment
let name = "world"
term.log(`Hello, $(name)!`)
```

Notice the backtick string above — this is a **template string**, and anything inside
`$( ... )` is evaluated as Argon code and interpolated into the string. See
[Strings](../../language/strings/README.md) for more.

Blocks in Argon are introduced with the `do` keyword rather than braces:

```
let add(a, b) = do
    return a + b

let x = 5
if (x > 3) do
    term.log("big")
else do
    term.log("small")
```

Whitespace/indentation is not significant for parsing (the `do ... ` block continues until
the parser determines it's finished based on structure), but you should still indent
consistently for readability — see the examples throughout this documentation.

## Next steps

- [Variables and `let`](../../language/variables/README.md)
- [Functions](../../language/functions/README.md)
- [Control flow](../../language/control-flow/README.md)