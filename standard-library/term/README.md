# `term`

`term` is a global (always available, no `import` needed) providing basic terminal I/O.

## `term.log(...)`

Prints any number of arguments, space-separated, followed by a newline — the main way to
print output:

```
term.log("Hello, world!")
term.log(1, 2, 3)
term.log(x, y, z)
```

Used throughout the test suite, e.g.
[`array.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array.ar):

```
let x = [0,1,2,3,4,5,6,7,8,9]
term.log(x[3])
```

## `term.error(...)`

Same as `term.log`, but writes to the error stream instead of standard output.

## `term.input(prompt)`

Reads a line of input from the user, optionally showing `prompt` first:

```
let name = term.input("What's your name? ")
term.log(`Hello, $(name)!`)
```

## Next

- [`maths`](../maths/README.md)