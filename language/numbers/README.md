# Numbers

Argon has a single `number` type. Numbers are **exact rationals** (backed by GMP), not
floating point, and integers have arbitrary size:

```argon
term.log(0.1 + 0.2 == 0.3)   # true
term.log((1 / 3) * 3)        # 1
term.log(2 ^ 100)            # 1267650600228229401496703205376
```

Non-integer results are *displayed* as decimals (rounded to 16 significant digits), but
the stored value stays exact:

```argon
let third = 1 / 3
term.log(third)              # 0.3333333333333333
term.log(third * 3 == 1)     # true
```

Operations that can't produce an exact rational, such as a fractional exponent, produce a
close decimal approximation:

```argon
term.log(2 ^ 0.5)   # 1.414213562373095
```

## Literals

| Form | Example | Value |
|---|---|---|
| integer | `42` | 42 |
| decimal | `3.14`, `.5` | 3.14, 0.5 |
| scientific | `1e6`, `1.5e-2` | 1000000, 0.015 |
| hexadecimal | `0xff` | 255 |
| octal | `0o17` | 15 |
| binary | `0b101` | 5 |

```argon
term.log(0xff, 0o17, 0b101, 1e3, .5)   # 255 15 5 1000 0.5
```

Negative numbers are written with unary minus (`-5`).

## Converting to and from numbers

`number(value)` converts a string (or anything with a `__number__` method) to a number.
`string(value)` converts a number to a string:

```argon
term.log(number("12.5") + 1)     # 13.5
term.log(number("0x10"))         # 16
term.log(string(42) + "!")       # 42!
```

`number(true)` is `1`, and `number(null)` and `number(false)` are `0`.

## Rounding

There are no rounding methods on numbers themselves; use the
[`maths`](../../standard-library/maths/README.md) module:

```argon
import "maths" as maths
term.log(maths.floor(2.7), maths.ceil(2.1), maths.round(2.5), maths.int(-2.7))   # 2 3 3 -2
```

## Division and remainders

`/` is exact division, `//` is integer division and `%` is the remainder. See
[Operators](../operators/README.md#arithmetic) for how they treat negative numbers.

Dividing by zero throws a `ZeroDivisionError`.

## Next

- [Operators](../operators/README.md)
