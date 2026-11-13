# `random`

```argon
import "random" expose random
```

The module provides a ready-made generator, `random`, seeded from the operating system's
source of randomness, and the `Random` class for creating your own generators.

## Methods

| Method | Description |
|---|---|
| `random.random()` | a random number in `[0, 1)` |
| `random.range(start, end)` | a random number in `[start, end)` |
| `random.int(start, end)` | a random integer in `[start, end)`: `end` is **excluded** |
| `random.choice(items)` | a random item from an array (or anything indexable with a `length`) |

```argon
import "random" expose random

let dice = random.int(1, 7)          # 1 to 6
let index = random.int(0, 10)        # 0 to 9
let colour = random.choice(["red", "green", "blue"])
term.log(dice, index, colour)
```

Shuffling an array (Fisher–Yates):

```argon
import "random" expose random

let shuffle(items) = do
    let i = items.length - 1
    while (i > 0) do
        let j = random.int(0, i + 1)
        let temp = items[i]
        items[i] = items[j]
        items[j] = temp
        i -= 1
    return items

term.log(shuffle(array(1 to 10)))
```

## Seeded generators

`Random(seed)` creates an independent generator. Two generators with the same seed produce
the same sequence, which is useful for reproducible results:

```argon
import "random" expose Random

let a = Random(42)
let b = Random(42)
term.log(a.random() == b.random())   # true
```

## Next

- [`path`](../path/README.md)
