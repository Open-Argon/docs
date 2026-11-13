# `url`

```argon
import "url" as url
```

Percent-encoding for URLs and query strings.

| Function | Description |
|---|---|
| `url.encode(value)` | percent-encode a value (converted to a string) for use in a query string (spaces become `+`) |
| `url.decode(text)` | reverse `encode` (`+` becomes a space, `%XX` is decoded) |
| `url.queryStr(dictionary)` | build a query string from a dictionary; values are converted to strings |
| `url.decodeURLQuery(text)` | parse a query string into a dictionary |

Letters, digits and `-`, `_`, `.`, `~` are left as they are; every other byte is encoded.

```argon
import "url" as url

term.log(url.encode("a b&c=d"))                   # a+b%26c%3Dd
term.log(url.decode("a+b%26c"))                   # a b&c
term.log(url.queryStr({q: "x y", page: 2}))       # q=x+y&page=2
term.log(url.decodeURLQuery("a=1&b=x+y"))         # {"a": "1", "b": "x y"}
```

## Next

- [`hex`](../hex/README.md)
