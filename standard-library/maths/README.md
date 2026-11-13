# `maths`

```argon
import "maths" as maths
```

Basic numeric helpers, written in Argon.

| Function | Description |
|---|---|
| `maths.abs(num)` | absolute value |
| `maths.int(num)` | integer part, rounding toward zero |
| `maths.floor(num)` | round down |
| `maths.ceil(num)` | round up |
| `maths.round(num, precision=1)` | round to the nearest multiple of `precision`, with halves rounding away from zero |
| `maths.max(*nums)` | the largest argument |
| `maths.min(*nums)` | the smallest argument |

```argon
import "maths" as maths

term.log(maths.abs(-5))            # 5
term.log(maths.int(-2.7))          # -2
term.log(maths.floor(1.7))         # 1
term.log(maths.floor(-1.2))        # -2
term.log(maths.ceil(1.2))          # 2
term.log(maths.round(2.5))         # 3
term.log(maths.round(1.26, 0.1))   # 1.3
term.log(maths.round(17, 5))       # 15
term.log(maths.max(3, 7, 2))       # 7
term.log(maths.min(3, 7, 2))       # 2
```

`maths.max` and `maths.min` take separate arguments, not an array. To use them with an
array, unpack it: `maths.max(*values)`. With no arguments they return `null`.

Numbers in Argon are exact rationals, so `maths.round(1.26, 0.1)` is exactly `1.3`. See
[Numbers](../../language/numbers/README.md).

## Next

- [`random`](../random/README.md)
