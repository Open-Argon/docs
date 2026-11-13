# The REPL

Start the REPL by running `argon` with no arguments in a terminal:

```bash
argon
```

```
Chloride v4.1.0 Copyright (C) 2026 William Bell
This program comes with ABSOLUTELY NO WARRANTY; for details type "license".
This is free software released under the GNU GPL Version 3 or later,
and you are welcome to redistribute it under certain conditions; type "license" for details.
You can learn more about what the term "free software" means at https://www.gnu.org/philosophy/free-sw.html

>>>
```

Enter code at the prompt. The result of each entry is printed unless it is `null`:

```
>>> let x = 1
1
>>> x += 1
2
>>> let f(x) = 2*x^2 + 3*x + 10
<function f at 0x7f6df3b9ee00>
>>> f(x)
24
>>> term.log("hi")
hi
```

## Multi-line input

When a line ends with `do`, the REPL continues on a new line with the next level of
indentation already filled in. Enter a blank line to finish the innermost block. The code
runs once every block is closed:

```
>>> let f(x) = do
...     x *= 10
...     return x
...
<function f at 0x7f6df3b9e000>
>>> f(2)
20
```

## Other details

- `license` prints the GNU GPL licence text.
- Press Ctrl+D to exit.
- Errors are printed without leaving the REPL.
- Variables persist between entries. Imports are resolved relative to the current
  directory.
- When standard input isn't a terminal (e.g. `echo 'term.log(1)' | argon`), `argon` runs the
  input as a program instead of starting the REPL.
