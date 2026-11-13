# Operators

## Arithmetic

| Operator | Meaning | Method |
|---|---|---|
| `+` | addition / string concatenation | `__add__` |
| `-` | subtraction | `__subtract__` |
| `*` | multiplication | `__multiply__` |
| `/` | exact division | `__division__` |
| `//` | integer division | `__floor_division__` |
| `%` | remainder | `__modulo__` |
| `^` | exponent | `__exponent__` |
| `-x` | negation (unary) | `__negation__` |

```argon
term.log(1 + 2)     # 3
term.log(7 / 2)     # 3.5
term.log(7 // 2)    # 3
term.log(7 % 2)     # 1
term.log(2 ^ 10)    # 1024
```

Note that the exponent operator is `^`, not `**`.

`//` always rounds **down**, and `%` gives a remainder with the same sign as the right
operand, so `a == (a // b) * b + a % b` always holds (the same rules as Python):

```argon
term.log((-7) // 2, (-7) % 2)    # -4 1
term.log(7 // (-2), 7 % (-2))    # -4 -1
term.log((-7.5) // 2)            # -4
```

The `+` operator only joins values of the same kind. Use a
[template string](../strings/README.md#template-strings) or `string()` to mix strings with
other values:

```argon
let n = 3
term.log("n is " + string(n))
term.log(`n is $(n)`)
```

Each arithmetic operator also has a global function form that applies the operator from
left to right across any number of arguments: `add`, `subtract`, `multiply`, `divide`,
`floor_divide`, `modulo`, `exponent`:

```argon
term.log(add(1, 2, 3))      # 6
term.log(subtract(10, 3))   # 7
term.log(exponent(2, 3))    # 8
```

## Comparison

| Operator | Meaning | Method | Function |
|---|---|---|---|
| `==` | equal | `__equal__` | `equal` |
| `!=` | not equal | `__not_equal__` | `not_equal` |
| `<` | less than | `__less_than__` | `less_than` |
| `<=` | less than or equal | `__less_than_equal__` | `less_than_equal` |
| `>` | greater than | `__greater_than__` | `greater_than` |
| `>=` | greater than or equal | `__greater_than_equal__` | `greater_than_equal` |

Numbers compare numerically, and strings compare lexicographically. Values of different
types are never equal (`1 == "1"` and `1 == true` are both `false`).

For arrays, dictionaries and objects, `==` compares **identity** unless the class defines
`__equal__`: two separately created arrays with the same contents are not equal.

```argon
let a = [1, 2]
term.log(a == a, [1, 2] == [1, 2])   # true false
```

## Logical operators

- `a && b` gives `a` if `a` is falsy, otherwise `b`.
- `a || b` gives `a` if `a` is truthy, otherwise `b`.
- `!a` gives `true` if `a` is falsy, otherwise `false`.

`&&` and `||` short-circuit: the right-hand side is only evaluated if it's needed. They
return one of their operands, not necessarily a boolean, which makes `||` handy for
defaults:

```argon
let name = null
term.log(name || "anonymous")   # anonymous
term.log(1 && 2, 0 && 2)        # 2 0
```

Note: `not` on its own is reserved but isn't an operator. Use `!`.

## Membership: `in` and `not in`

```argon
term.log("ell" in "hello")      # true, substring check
term.log(3 in [1, 2, 3])        # true
term.log("key" in {key: 1})     # true, checks dictionary keys
term.log(4 not in [1, 2, 3])    # true
term.log(4 in 1 to 10)          # true, see ranges below
```

`in` calls the right-hand value's `__contains__` method, so your own classes can support
it too.

## Conditional (ternary) expression

```argon
let a = 1
let b = 2
let order = a < b ? -1 : a > b ? 1 : 0
term.log(order)   # -1
```

## Ranges: `to` and `until`

`start to stop` is an **inclusive** range, and `start until stop` excludes `stop`. Both are
most commonly used with `for`. The bounds can be any arithmetic expression:

```argon
let n = 3
for (i in 1 to 3) term.log(i)        # 1, 2, 3
for (i in 0 until 3) term.log(i)     # 0, 1, 2
term.log(array(0 until 5))           # [0, 1, 2, 3, 4]
term.log(array(-1 to n - 1))         # [-1, 0, 1, 2]
```

See [`for` loops](../control-flow/for/README.md#ranges) for the `range()` function, which
also supports a step.

## Indexing, calling and attribute access

| Syntax | Meaning | Method |
|---|---|---|
| `obj.name` | attribute access | `__getattribute__` / `__getattr__` |
| `obj[key]` | item access | `__getitem__` |
| `obj[key] = value` | item assignment | `__setitem__` |
| `delete obj[key]` | item deletion | `__delitem__` |
| `obj(args)` | call | `__call__` |

## Precedence

From highest (binds tightest) to lowest:

1. Calls `f()`, indexing `a[i]` and attribute access `a.b`
2. `^` (groups **right to left**)
3. Unary `-x` and `!x`
4. `*`, `/`, `//`, `%`
5. `+`, `-`
6. Ranges: `to`, `until`
7. `<`, `>`, `<=`, `>=`, `in`, `not in`
8. `==`, `!=`
9. `&&`
10. `||`
11. `? :`
12. Assignment: `=`, `+=`, `-=`, ...

Operators on the same level group left to right, except `^`.

```argon
term.log(1 + 2 * 3)    # 7
term.log(8 / 2 * 2)    # 8, because (8 / 2) * 2
term.log(10 - 2 + 3)   # 11
term.log(2 * 3 ^ 2)    # 18
term.log(2 ^ 3 ^ 2)    # 512, because 2 ^ (3 ^ 2)
term.log(-2 ^ 2)       # -4, because -(2 ^ 2)
term.log(2 ^ -1)       # 0.5
```

Unary minus binds less tightly than `^` (like in maths and Python), so write `(-2) ^ 2`
to square a negative number.

Ranges sit between arithmetic and comparisons, so `0 until n + 1` means
`0 until (n + 1)`, `-1 to 3` starts at `-1`, and `x in 1 to 10` means `x in (1 to 10)`.

## Next

- [Control flow](../control-flow/README.md)
