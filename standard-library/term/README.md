# `term`

`term` is a global (always available, no `import` needed) for basic terminal I/O.

## `term.log(*values, sep=" ", end="\n")`

Prints its arguments to standard output, converted to strings and separated by `sep`,
followed by `end`. Output is flushed immediately.

```argon
term.log("Hello, world!")
term.log(1, 2, 3)                         # 1 2 3
term.log(1, 2, 3, sep = ", ")             # 1, 2, 3
term.log("no newline", end = "")
term.log()
```

Values are converted with their `__string__` method, so strings print without quotes.
Inside arrays and dictionaries, strings are shown quoted:

```argon
term.log("text", ["text"], {key: "text"})   # text ["text"] {"key": "text"}
```

## `term.error(exception)`

Prints an exception to standard error in the same format used for uncaught errors,
without stopping the program. It only accepts exception objects:

```argon
try throw ValueError("something went wrong")
catch (Exception as e) term.error(e)
```

To print arbitrary text to standard error, write to
[`file.stderr`](../file/README.md#standard-streams).

## `term.input(prompt?)`

Shows `prompt` (if given), reads a line from the user and returns it as a string, without
the trailing newline:

```argon
let name = term.input("What's your name? ")
term.log(`Hello, $(name)!`)
```

## Next

- [`maths`](../maths/README.md)
