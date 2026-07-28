# Arrays

Arrays are ordered, mutable, heterogeneous collections written with `[ ]`:

```
let x = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
term.log(x[3])   # 3
```

(from [`array.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array.ar))

## Indexing and slicing

Arrays (and strings) support Python-style slicing: `array[start:stop:step]`, with negative
indices counting from the end:

```
let x = [0,1,2,3,4,5,6,7,8,9]

term.log(x[1:])      # [1,2,3,4,5,6,7,8,9]
term.log(x[:5])      # [0,1,2,3,4]
term.log(x[:5:2])    # [0,2,4]
term.log(x[::-1])    # reversed: [9,8,7,6,5,4,3,2,1,0]
term.log(x[-1::])    # [9]
term.log(x[-1:2:])   # [] (start after stop, forward step)
term.log(x[-1::-1])  # reversed from the end
```

(from [`array_slice.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array_slice.ar))

Any part of the slice (`start`, `stop`, or `step`) can be omitted.

## Multiple slice arguments

Custom classes can implement `__getitem__`/`__setitem__` to accept multiple comma-separated
arguments in the subscript, e.g.:

```
class x do
    this.__getitem__(self, key) = key
    this.__setitem__(self, key, value) = key

term.log(x()[1:2, 2])
term.log(x()[1:2, 2] = 1)
```

(from [`slice.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/slice.ar)) — here `key` receives a tuple
representing both the slice `1:2` and the plain index `2`.

## Mutation

```
let x = []
x.append(1)
x.append(2)
x[0] = 10
```

## Built-in array methods

| Member | Description |
|---|---|
| `.length` | number of elements |
| `.append(value)` | add an element to the end |
| `.insert(index, value)` | insert at a position |
| `.pop(index)` | remove and return the element at `index` (or the last element) |
| `.join(sep)` | join elements into a string, separated by `sep` |
| `.map(fn)` | return a new array with `fn` applied to each element |
| `.filter(fn)` | return a new array of elements where `fn` returns truthy |
| `.sort(fn)` | sort in place using a comparator function |
| `in` | membership test |
| `[start:stop:step]` | slicing (see above) |

### `.pop`

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

(from [`array_pop.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array_pop.ar)) — this shuffles `x` into `y`
by repeatedly popping a random element.

### `.map`, `.filter`, `.sort` (chained)

```
term.log(
  array(range(-1000000, 1000000))
    .map((val) = val * val)
    .filter((val) = val < 10000)
    .sort((a, b) = a < b ? -1 : a > b ? 1 : 0)
)
```

(from [`map_filter_sort.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/map_filter_sort.ar)) — `range(...)`
produces a range object, and `array(...)` converts it to a concrete array first.

### Random shuffle via swapping

```
import "../stdlib/random" expose random

let x = []
let i = 0

while (i < 1e5) do
    x.append(i)
    i = i + 1

i = 0
let len = x.length
while (i < len) do
    let random_index = random.int(0, len - 1)
    let temp = x[len - 1]
    x[len - 1] = x[random_index]
    x[random_index] = temp
    i = i + 1

term.log(x)
```

(from [`array_set.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array_set.ar)) — shows direct index
assignment (`x[i] = value`) used to implement a Fisher–Yates-style shuffle.

## Iteration

Arrays are iterable with `for`:

```
for (item in [1, 2, 3]) do
    term.log(item)
```

See [`for` loops](../control-flow/for/README.md) for the underlying iterator protocol.

## Next

- [Dictionaries and tuples](../dictionaries-and-tuples/README.md)