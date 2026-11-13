# `json`

```argon
import "json" as json
```

A JSON encoder and decoder written in Argon.

## `json.stringify(value, pretty=false, indent="    ")`

Converts a value to a JSON string. Supported values are `null`, booleans, numbers, strings,
arrays, tuples (written as JSON arrays) and dictionaries:

```argon
import "json" as json

term.log(json.stringify(null))                    # null
term.log(json.stringify([1, 2, 3]))               # [1,2,3]
term.log(json.stringify({a: 1, b: [true, "x"]}))  # {"a":1,"b":[true,"x"]}
```

With `pretty=true`, the output is spread over multiple lines and indented with `indent`:

```argon
import "json" as json

term.log(json.stringify({name: "argon", tags: ["fast"]}, pretty = true, indent = "  "))
```

```
{
  "name": "argon",
  "tags": [
    "fast"
  ]
}
```

Details:

- Dictionary keys that aren't strings are converted to strings: `{1: "a"}` becomes
  `{"1":"a"}`.
- Non-ASCII characters are escaped as `\uXXXX` (with surrogate pairs for characters
  outside the Basic Multilingual Plane, like emoji).
- Non-integer numbers are written as decimals with up to 16 significant digits.
- An object whose class defines `__json__(self, pretty, parents)` is serialised by calling
  it; it should return a JSON string.
- Other types (such as class instances without `__json__`) throw a `TypeError`.
- Circular references throw a `ValueError`.

## `json.parse(text)`

Parses a JSON string into Argon values. Objects become dictionaries, so their members can be
read with `.`:

```argon
import "json" as json

let data = json.parse("{\"name\": \"argon\", \"versions\": [3, 4], \"stable\": null}")
term.log(data.name, data.versions[1], data.stable)   # argon 4 null
```

All standard JSON escapes are supported, including `\uXXXX` and surrogate pairs.

Malformed JSON throws a `ValueError` describing the problem:

```argon
import "json" as json

try json.parse("[1, 2,]")
catch (ValueError as e) term.log(e.message)   # Unexpected character ']' at position 6
```

## Next

- [`date`](../date/README.md)
