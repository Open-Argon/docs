# Strings

Strings can be written with single or double quotes:

```
let a = "hello"
let b = 'hello'
```

## Template strings

Backtick strings support interpolation via `$( ... )`, where anything inside the
parentheses is evaluated as Argon code:

```
let wow = "this is cool"

term.log(
  `hello world

$(
  do
    term.log("wow")
    return wow
)`
)
```

(from [`templates.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/templates.ar)) — this demonstrates that the
interpolated expression can be an entire `do` block with side effects, not just a simple
variable.

A simpler, more common case:

```
let name = "world"
term.log(`Hello, $(name)!`)
```

Template strings also support unicode escapes like `\u1324`:

```
throw MyError(`hel\u1324lo`)
```

(from [`error_system.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/error_system.ar))

## Escape sequences

Standard escapes are supported inside strings, including `\"`, `\\`, `\/`, `\b`, `\f`,
`\n`, `\r`, `\t`, and `\uXXXX` (see the JSON standard library's string parser at
[`stdlib/json/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea43json/init.ar) for the full escape table
used when parsing JSON strings — the same escapes are valid in Argon string literals).

## Indexing and slicing

Strings support the same indexing/slicing syntax as arrays — see
[Arrays](../arrays/README.md#indexing-and-slicing) for the full slice syntax
(`s[1:]`, `s[:5]`, `s[::-1]`, negative indices, step values, etc).

```
let s = "hello"
term.log(s[0])       # "h"
term.log(s[-1])      # "o"
term.log(s[1:3])     # "el"
```

## Built-in string methods

Strings support (at minimum) the following built-in methods and properties, based on the
interpreter's string type implementation:

| Member | Description |
|---|---|
| `.length` | number of characters |
| `.upper()` | uppercase copy |
| `.lower()` | lowercase copy |
| `.title()` | title-cased copy |
| `.replace(old, new)` | replace all occurrences |
| `.split(sep)` | split into an array of strings |
| `.strip()` | strip surrounding whitespace |
| `.index_of(sub)` | index of a substring |
| `.chr()` / `.ord()` | character/code-point conversion |
| `in` | substring membership test (`"ell" in "hello"`) |
| `[start:stop:step]` | slicing |

```
let __starts_with__(s, prefix) = do
    if (prefix.length > s.length) do
        return false
    return s[0:prefix.length] == prefix
```

(from [`stdlib/path/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea43path/init.ar), showing `.length` and
slicing used together)

## Comparing strings

Strings support the usual comparison operators (`==`, `!=`, `<`, `<=`, `>`, `>=`), which
compare lexicographically.

## Joining strings

Arrays of strings can be joined with `.join(sep)` — see
[Arrays](../arrays/README.md#join).

## Converting to a string

Every object has a `__string__()` method used for display/printing, and calling
`string(value)` converts any value to its string form:

```
term.log(string(123))     # "123"
```

## Next

- [Arrays](../arrays/README.md)