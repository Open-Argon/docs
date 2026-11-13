# Buffers

A `buffer` is a mutable sequence of raw bytes. Buffers are used for binary data: files
opened in binary mode, the standard streams in the [`file`](../../standard-library/file/README.md)
module, network sockets and HTTP bodies.

## Creating buffers

```argon
let a = buffer.from_string("hello")   # bytes of a UTF-8 string
let b = buffer.of_size(16)            # 16 zero bytes
term.log(a.length, b.length)          # 5 16
```

## Reading bytes

Indexing returns a byte as a number (0 to 255), and slicing returns a new buffer:

```argon
let data = buffer.from_string("hello world")
term.log(data[0])                  # 104
term.log(data[0:5].to_string())    # hello
```

`to_string()` decodes the buffer as a string.

## Methods

| Member | Description |
|---|---|
| `.length` | number of bytes |
| `.to_string()` | the bytes as a string |
| `.extend(other)` | append another buffer's bytes in place |
| `.split(separator)` | array of buffers, split on a separator buffer |
| `.partition(separator)` | `[before, separator, after]` around the first occurrence; if it isn't found, the separator part is empty |
| `buffer.from_string(s)` | new buffer containing the bytes of `s` |
| `buffer.of_size(n)` | new buffer of `n` zero bytes |

```argon
let data = buffer.from_string("key: value")
let parts = data.partition(buffer.from_string(": "))
term.log(parts[0].to_string(), parts[2].to_string())   # key value

let joined = buffer.of_size(0)
joined.extend(buffer.from_string("ab"))
joined.extend(buffer.from_string("cd"))
term.log(joined.to_string())   # abcd
```

## Next

- [Classes and objects](../classes/README.md)
