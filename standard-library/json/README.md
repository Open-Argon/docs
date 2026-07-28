# `json`

```
import "json" as json
```

A pure-Argon JSON encoder/decoder (see
[`stdlib/json/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea43json/init.ar)).

## `json.stringify(value, pretty=false)`

Converts an Argon value into a JSON string.

```
term.log(json.stringify(null))                 # "null"
term.log(json.stringify(true))                  # "true"
term.log(json.stringify("hello"))               # "\"hello\""
term.log(json.stringify([1, 2, 3]))              # "[1,2,3]"
term.log(json.stringify({"a": 1, "b": 2}))       # "{\"a\":1,\"b\":2}"
term.log(json.stringify({"a": [1, 2, 3]}))       # "{\"a\":[1,2,3]}"
```

Dictionary keys that aren't strings are coerced to their string form:

```
term.log(json.stringify({1: "a"}))       # {"1":"a"}
term.log(json.stringify({true: "a"}))    # {"true":"a"}
```

Non-ASCII characters are escaped using `\uXXXX` (including surrogate pairs for characters
outside the Basic Multilingual Plane, like emoji):

```
term.log(json.stringify("🇬🇧"))   # "\"\\uD83C\\uDDEC\\uD83C\\uDDE7\""
```

Circular references raise a `ValueError`:

```
let a = []
a.append(a)
json.stringify(a)   # throws ValueError("circular JSON serialisation")
```

## `json.parse(s)`

Parses a JSON string into Argon values (`null`/`true`/`false`, numbers, strings, arrays,
and dictionaries):

```
term.log(json.parse("null"))                  # null
term.log(json.parse("[1,2,3]"))               # [1, 2, 3]
term.log(json.parse("{\"a\":1,\"b\":2}"))     # {a: 1, b: 2}
```

Whitespace around tokens is tolerated:

```
term.log(json.parse(" [ 1 , 2 , 3 ] "))   # [1, 2, 3]
```

All standard JSON string escapes are supported when parsing, including `\n`, `\t`, `\\`,
`\/`, `\b`, `\f`, `\r`, and `\uXXXX` (with correct surrogate-pair handling for characters
outside the Basic Multilingual Plane).

### Errors

Malformed JSON raises a `ValueError`, for example:

```
json.parse("")                 # ValueError — empty input
json.parse("{")                # ValueError — unterminated object
json.parse("[1,2,]")           # ValueError — trailing comma
json.parse("\"unterminated")   # ValueError — unterminated string
json.parse("tru")              # ValueError — truncated keyword
json.parse("1 2")              # ValueError — trailing content after value
json.parse("\"\\uD800\"")      # ValueError — lone (unpaired) surrogate
```

## Round-tripping

`json.parse(json.stringify(value))` reproduces `value` for all JSON-representable data —
this is exercised extensively in
[`tests/json_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/json_test.ar), which is also a good
reference for exact expected output formatting.

## Next

- [`file`](../file/README.md)