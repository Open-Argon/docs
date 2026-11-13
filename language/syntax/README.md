# Syntax basics

## Statements and lines

Each statement goes on its own line. There are no semicolons.

```argon
let x = 1
let y = 2
term.log(x + y)
```

An expression may continue over several lines when the line ends in the middle of it, for
example after a binary operator or a comma, or inside brackets:

```argon
let total = 1 +
    2 +
    3

let numbers = [
    1,
    2,
    3,
]

let doubled = numbers
    .map((n) = n * 2)
    .filter((n) = n > 2)

term.log(total, doubled)   # 6 [4, 6]
```

A trailing comma is allowed in array literals.

## Comments

`#` starts a comment that runs to the end of the line. There are no block comments.

```argon
# a whole-line comment
let x = 10   # a trailing comment
```

## Blocks: `do`

A block is opened with `do` at the **end of a line**. The lines that follow, indented
deeper than the line containing `do`, form the block's body. The block ends at the first
line that returns to the outer indentation:

```argon
let x = 5
if (x > 0) do
    term.log("positive")
    term.log("still in the block")
term.log("after the block")
```

Rules:

- Nothing may follow `do` on the same line (apart from a comment).
- Every line in a block must use the same indentation. A line indented less than the
  block but more than the enclosing code is a `SyntaxError: unexpected indent`.
- Any amount of indentation works, but four spaces is the convention used throughout the
  standard library and this documentation.
- An empty `do` (with no indented body) is allowed, e.g. `class Empty do`.

Nested blocks indent further:

```argon
let n = 3
if (n > 0) do
    term.log("positive")
    if (n > 2) do
        term.log("and bigger than 2")
    term.log("back in the outer block")
```

### Single-statement bodies

Anywhere a block is expected (`if`, `else`, `while`, `for`, `try`, `catch`, function and
class bodies), you can write a single statement or expression instead of a `do` block:

```argon
let x = 5
if (x > 3) term.log("big")
else term.log("small")

let square(n) = n * n
```

### `do` blocks are expressions

A `do` block is itself an expression. Its value is whatever it `return`s (or `null`), and
it has its own scope:

```argon
let label = do
    let n = 42
    if (n > 10) return "large"
    return "small"

term.log(label)   # large
```

This is how multi-statement function bodies work: `let f() = do ...` assigns a function
whose body is a `do` block.

## Identifiers

Identifiers start with a letter or `_`, followed by letters, digits or `_`. They are case
sensitive.

### Reserved words

The following words are keywords and can't be used as identifiers:

```
if  else  while  forever  for  break  continue  return  let  class  import  expose
to  until  as  from  do  true  false  null  delete  not  in  try  catch  throw
```

`from` is reserved but not currently used, and `not` is only used as part of `not in`.

## Next

- [Variables](../variables/README.md)
