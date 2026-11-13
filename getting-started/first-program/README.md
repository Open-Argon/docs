# Your first program

Create a file called `hello.ar`:

```argon
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

`term` is a global that is always available, so printing doesn't need an `import`.

## A quick tour

Argon files use the `.ar` extension. Comments start with `#` and run to the end of the
line:

```argon
# this is a comment
let name = "world"
term.log(`Hello, $(name)!`)   # prints: Hello, world!
```

The backtick string above is a **template string**: anything inside `$( ... )` is evaluated
as Argon code and inserted into the string. See [Strings](../../language/strings/README.md)
for more.

Blocks are opened with the `do` keyword at the end of a line, and the indented lines below
it form the body. The block ends when the indentation returns to the previous level:

```argon
let add(a, b) = do
    return a + b

let x = 5
if (x > 3) do
    term.log("big")
else do
    term.log("small")

term.log(add(x, 10))   # 15
```

Indentation **is** significant inside a `do` block, so every line of a block must use the
same indentation (any amount of spaces works, as long as it's consistent). See
[Syntax basics](../../language/syntax/README.md) for the full rules.

When a body is a single statement, `do` isn't needed at all:

```argon
let x = 5
if (x > 3) term.log("big")
let square(n) = n * n
term.log(square(x))   # 25
```

## Next steps

- [Syntax basics](../../language/syntax/README.md)
- [Variables](../../language/variables/README.md)
- [Functions](../../language/functions/README.md)
- [Control flow](../../language/control-flow/README.md)
