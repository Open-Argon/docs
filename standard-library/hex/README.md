# `hex`

```argon
import "hex" expose hex
```

## `hex(n)`

Formats an integer as an uppercase hexadecimal string, without a `0x` prefix. Negative
numbers get a leading `-`, and non-integers throw a `ValueError`:

```argon
import "hex" expose hex

term.log(hex(255))    # FF
term.log(hex(4096))   # 1000
term.log(hex(-255))   # -FF
term.log(hex(0))      # 0
```

To go the other way, use `number("0x" + text)`:

```argon
term.log(number("0xFF"))   # 255
```

## Next

- [`threading`](../threading/README.md)
