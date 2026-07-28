# `random`

```
import "../stdlib/random" expose random
```

The module exposes a single ready-made `random` instance (of an internal `Random` class),
seeded from the OS's source of randomness by default:

```
class Random do
  this.__init__(self, seed) = do
    self.__state__ = __rand__.random_seed(seed)
  this.random(self) = do
    return __rand__.random_next_number(self.__state__)
  this.range(self, start, end) = do
    return start + self.random()*end
  this.int(self, start, end) = do
    return maths.int(self.range(start, end))
  this.choice(self, arr) = do
    return arr[self.int(0, arr.length)]

let random = Random(__rand__.random_os_seed())
```

(from [`stdlib/random/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/random/init.ar)) — the underlying
random number generation is implemented in native C (`native/random.c`), loaded via
`load_native_code`.

## Reference

| Member | Description |
|---|---|
| `random.random()` | a random floating point number in `[0, 1)` |
| `random.range(start, end)` | a random float between `start` and `end` |
| `random.int(start, end)` | a random integer between `start` and `end` (inclusive-ish, see usage below) |
| `random.choice(arr)` | a random element from `arr` |

## Examples

Picking a random index and popping it (a shuffle pattern):

```
import "../stdlib/random" expose random

let x = []
let i = 0
while (i < 1e6) do
    x.append(i)
    i = i + 1

let y = []
while (x.length) do
    let result = x.pop(random.int(0, x.length - 1))
    y.append(result)
```

(from [`array_pop.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array_pop.ar))

Swapping random elements (Fisher–Yates style shuffle):

```
let random_index = random.int(0, len - 1)
let temp = x[len - 1]
x[len - 1] = x[random_index]
x[random_index] = temp
```

(from [`array_set.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array_set.ar))

## Creating your own generator

You can also construct your own seeded `Random`-like instance if you need reproducibility,
though the standard library's `random` export is a single shared instance seeded from the
OS by default — check the `random_test.ar` test file in the repository if you need to see
further custom-seeding examples.

## Next

- [`path`](../path/README.md)