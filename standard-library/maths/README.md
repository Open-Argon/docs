# `maths`

```
import "maths" as maths
```

The full implementation, from
[`stdlib/maths/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea43maths/init.ar):

```
let abs(num) = do
  if (num<0) return -num
  return num

let int(num) = do
  if (num<0) return -((-num)//1)
  return num//1

let floor(num) = do
    let i = int(num)
    if (num < 0 && num != i) do
        return i - 1
    return i

let ceil(num) = do
    let i = int(num)
    if (num > 0 && num != i) do
        return i + 1
    return i

let round(num, precision=1) = do
    num = num/precision
    if (num >= 0) do
        return floor(num + 0.5)*precision
    else do
        return ceil(num - 0.5)*precision

let max(*nums) = do
    let biggest = null
    for (num in nums) do
        if (biggest == null || num > biggest) do
            biggest = num
    return biggest

let min(*nums) = do
    let smallest = null
    for (num in nums) do
        if (smallest == null || num < smallest) do
            smallest = num
    return smallest
```

## Reference

| Function | Description |
|---|---|
| `maths.abs(num)` | absolute value |
| `maths.int(num)` | truncate towards zero |
| `maths.floor(num)` | round down |
| `maths.ceil(num)` | round up |
| `maths.round(num, precision=1)` | round to the nearest multiple of `precision` |
| `maths.max(*nums)` | largest of any number of arguments |
| `maths.min(*nums)` | smallest of any number of arguments |

```
term.log(maths.abs(-5))     # 5
term.log(maths.floor(1.7))  # 1
term.log(maths.ceil(1.2))   # 2
term.log(maths.round(1.25, 0.5))
term.log(maths.max(3, 7, 2))  # 7
term.log(maths.min(3, 7, 2))  # 2
```

Note `maths.max`/`maths.min` take a variadic list of arguments (`*nums`), not an array —
see [Functions: variadic arguments](../../language/functions/README.md#variadic-arguments-args-and-kwargs).

## Next

- [`random`](../random/README.md)