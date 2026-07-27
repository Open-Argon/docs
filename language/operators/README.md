# Operators

## Arithmetic

| Operator | Meaning         |
|----------|-----------------|
| `+`      | addition        |
| `-`      | subtraction / unary negation |
| `*`      | multiplication  |
| `/`      | division        |
| `//`     | floor division  |
| `%`      | modulo          |
| `**`     | exponent (based on the `EXPONENT_FUNCTION` builtin) |

```
term.log(1 + 2)     # 3
term.log(7 // 2)    # 3
term.log(7 % 2)     # 1
```

Every arithmetic operator is backed by a global function you can also call directly, and
which classes can override by implementing the matching dunder method (e.g. `__add__`):

```
term.log(add(1, 2))         # 3
term.log(subtract(5, 2))    # 3
term.log(multiply(2, 3))    # 6
term.log(divide(6, 2))      # 3
term.log(floor_divide(7, 2))# 3
term.log(modulo(7, 2))      # 1
term.log(exponent(2, 3))    # 8
```

Numeric literals also support scientific notation and hex, e.g. `1e5`, `1e7`, `0xD800`
(see [`array_set.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array_set.ar) for `1e5` and
[`json/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/json/init.ar) for hex literals).

## Comparison

| Operator | Meaning |
|----------|---------|
| `==`     | equal |
| `!=`     | not equal |
| `<`      | less than |
| `<=`     | less than or equal |
| `>`      | greater than |
| `>=`     | greater than or equal |

These also have global function equivalents: `equal`, `not_equal`, `less_than`,
`less_than_equal`, `greater_than`, `greater_than_equal` — and each is backed by a dunder
method (`__equal__`, `__less_than__`, etc.) that a class can override, e.g. the standard
`number` and `string` types both implement their own comparison dunders.

## Logical operators

```
a && b     # logical AND
a || b     # logical OR   (used in stdlib code, e.g. path.ar's `while ("//" in p)` style checks)
!a         # logical NOT
```

See [`my_first_calculator.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/my_first_calculator.ar) for repeated
use of `&&` in condition chains.

## The `in` operator

Test membership in a string, array, or dictionary:

```
term.log("ell" in "hello")   # true, substring check
term.log(3 in [1,2,3])       # true
term.log("key" in {key: 1})  # true, checks dictionary keys
```

This is backed by the `__contains__` dunder method, which is implemented on strings,
arrays, and dictionaries — and can be implemented on your own classes too.

## Ternary conditional expression

```
let result = a < b ? -1 : a > b ? 1 : 0
```

This is the pattern used in
[`map_filter_sort.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/map_filter_sort.ar) to write a comparator
function in one line.

## String concatenation

`+` also works on strings:

```
term.log("hello " + "world")
```

## Ranges

`a until b` (as seen in
[`range_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/range_test.ar)) produces an iterable range, most
commonly used with `for`:

```
for (x in 0 until 1e7) do
    ...
```

See [`for` loops](../control-flow/for/README.md) for more on ranges and the built-in
`range()` function.

## Unary operators

```
-x     # negation
!x     # logical not
```

## Next

- [Control flow](../control-flow/README.md)