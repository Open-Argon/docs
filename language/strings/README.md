# Strings

Strings can be written with double or single quotes. They are immutable.

```argon
let a = "hello"
let b = 'hello'
term.log(a == b)   # true
```

Quoted strings must fit on one line. For multi-line text, use a template string.

## Escape sequences

All kinds of string support the same escapes:

| Escape | Meaning |
|---|---|
| `\n` | newline |
| `\t` | tab |
| `\r` | carriage return |
| `\b` | backspace |
| `\f` | form feed |
| `\\` | backslash |
| `\/` | forward slash |
| `\"`, `\'`, `` \` `` | a double quote, single quote or backtick |
| `\$` | a dollar sign (use `\$(` for a literal `$(` in a template string) |
| `\uXXXX` | a Unicode code point; surrogate pairs like `\ud83d\ude00` (😀) are combined |

Any other backslash sequence is a `SyntaxError`.

```argon
term.log("tab:\there", 'it\'s', "caf\u00e9", "\ud83d\ude00")
```

## Template strings

Backtick strings support interpolation with `$( ... )`. The contents are evaluated as an
Argon expression and converted to a string:

```argon
let name = "world"
let items = [1, 2, 3]
term.log(`Hello, $(name)! You have $(items.length) items.`)
```

Template strings can span multiple lines, and the interpolated expression can even be a
`do` block:

```argon
let total = 7
term.log(`Report
total: $(total)
status: $(do
    if (total > 5) return "high"
    return "low"
)`)
```

A `$` that isn't followed by `(` is literal. To write a backtick or a literal `$(`, escape
them:

```argon
term.log(`costs $5, a \` backtick, and a literal \$(not code)`)
```

### Tagged templates

Put an object directly before a template string to pass the template's pieces to that
object's `__template__` method instead of building a string. The method receives a tuple of
`tuple(is_text, value)` pairs: text segments with `is_text` set to `true`, and interpolated
values with `is_text` set to `false`:

```argon
class Shout do
    this.__template__(self, parts) = do
        let out = ""
        for ([is_text, value] in parts) do
            if (is_text) out += value
            else out += string(value).upper()
        return out

let shout = Shout()
let name = "world"
term.log(shout`hello $(name)!`)   # hello WORLD!
```

## Strings are UTF-8 bytes

Strings are stored as UTF-8, and **`length`, indexing, slicing and iteration all work on
bytes**, not characters. This only matters for non-ASCII text:

```argon
term.log("abc".length)   # 3
term.log("é".length)     # 2
term.log("😀".length)    # 4
```

## Indexing and slicing

Strings support the same indexing and slicing syntax as arrays
(see [Arrays](../arrays/README.md#indexing-and-slicing)), with negative indices counting from
the end:

```argon
let s = "hello"
term.log(s[0])       # h
term.log(s[-1])      # o
term.log(s[1:3])     # el
term.log(s[::-1])    # olleh
```

## Concatenation and conversion

`+` joins two strings. Other types must be converted first with `string(value)`, which
works on every value:

```argon
term.log("a" + "b")                   # ab
term.log("count: " + string(3))       # count: 3
term.log(string([1, "x"]))            # [1, "x"]
term.log(string(null), string(true))  # null true
```

Converting a string to a number uses `number(value)`:

```argon
term.log(number("42") + 1)   # 43
```

## Comparison

Strings support `==`, `!=`, `<`, `<=`, `>` and `>=`, which compare byte by byte
(lexicographically). `in` and `not in` check for substrings:

```argon
term.log("apple" < "banana")    # true
term.log("ell" in "hello")      # true
```

## String methods

| Member | Description |
|---|---|
| `.length` | length in bytes |
| `.upper()` / `.lower()` | upper- / lowercase copy |
| `.title()` | copy with the first letter of each word capitalised |
| `.replace(old, new)` | replace every occurrence of `old` |
| `.repeat(n)` | the string repeated `n` times |
| `.split(sep, max_splits?)` | split into an array of strings on `sep` (must be non-empty) |
| `.strip(chars)` | remove any of the characters in `chars` from both ends |
| `.starts_with(prefix)` / `.ends_with(suffix)` | `true` / `false` |
| `.index_of(sub, from?)` | byte index of the first occurrence of `sub`, or `-1` |
| `.last_index_of(sub, from?)` | byte index of the last occurrence of `sub`, or `-1` |
| `.ord()` | Unicode code point of a single-character string |
| `.to_byte()` | numeric value of a single-byte string |
| `string.chr(code_point)` | the one-character string for a code point |
| `string.from_byte(byte)` | a one-byte string from a number 0 to 255 |

```argon
let s = "Hello, World"
term.log(s.upper(), s.lower())             # HELLO, WORLD hello, world
term.log("hello world".title())            # Hello World
term.log(s.replace("l", "L"))              # HeLLo, WorLd
term.log("ab".repeat(3))                   # ababab
term.log("a,b,c".split(","))               # ["a", "b", "c"]
term.log("a,b,c".split(",", 1))            # ["a", "b,c"]
term.log("  padded  ".strip(" "))          # padded
term.log("--x--".strip("-"))               # x
term.log(s.index_of("o"), s.index_of("o", 5), s.last_index_of("o"))   # 4 8 8
term.log("A".ord(), string.chr(128512))    # 65 😀
```

`strip` always needs the set of characters to remove. To trim ordinary whitespace, pass
`" \t\n\r"`.

To join an array of strings, use the array's [`join`](../arrays/README.md#array-methods)
method: `["a", "b"].join(", ")`.

## Next

- [Arrays](../arrays/README.md)
