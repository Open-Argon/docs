# Arrays

Arrays are ordered, mutable collections that can hold values of any type:

```argon
let x = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
let mixed = [1, "two", [3], {four: 4}]
term.log(x[3], x.length)   # 3 10
```

## Indexing and slicing

Indexes start at `0`. Negative indexes count from the end. Indexing past the end throws an
`IndexError`.

Slices use `array[start:stop:step]`, like Python. Any part can be omitted, and the result
is a new array:

```argon
let x = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

term.log(x[-1])      # 9
term.log(x[1:])      # [1, 2, 3, 4, 5, 6, 7, 8, 9]
term.log(x[:5])      # [0, 1, 2, 3, 4]
term.log(x[:5:2])    # [0, 2, 4]
term.log(x[::-1])    # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
term.log(x[-3:])     # [7, 8, 9]
```

A slice can also be created as a value with the `slice` type, and used as an index:

```argon
let s = slice(1, 5, 2)
term.log([0, 1, 2, 3, 4, 5][s])   # [1, 3]
```

## Changing arrays

```argon
let x = [1, 2, 3]
x[0] = 10           # replace an item
x.append(4, 5)      # add to the end
x.insert(1, "a")    # insert before index 1
delete x[2]         # remove an item
x[0:2] = ["z"]      # replace a slice
term.log(x)         # ["z", 3, 4, 5]
```

`length` is read-only: assigning to it throws an error.

## Array methods

| Member | Description |
|---|---|
| `.length` | number of items |
| `.append(*values)` | add values to the end; returns the array |
| `.insert(index, *values)` | insert values before `index`; returns the array |
| `.pop(index?)` | remove and return the item at `index` (default: the last item) |
| `.join(sep)` | join an array of strings into one string |
| `.map(fn)` | new array with `fn(item)` applied to each item |
| `.filter(fn)` | new array of the items for which `fn(item)` is truthy |
| `.sort(compare)` | sort **in place** with a comparator; returns the array |
| `array.of_size(n)` | new array of `n` `null`s |

```argon
let nums = [5, 3, 8, 1]
term.log(nums.map((n) = n * 2))             # [10, 6, 16, 2]
term.log(nums.filter((n) = n > 2))          # [5, 3, 8]
term.log(nums.sort((a, b) = a - b))         # [1, 3, 5, 8]
term.log(nums.pop(), nums.pop(0), nums)     # 8 1 [3, 5]
term.log(["a", "b", "c"].join(", "))        # a, b, c
```

`sort` always needs a comparator function. It should return a negative number if `a` comes
first, a positive number if `b` comes first, and `0` if they're equal. For strings, use a
comparison:

```argon
let words = ["pear", "apple", "fig"]
words.sort((a, b) = a < b ? -1 : a > b ? 1 : 0)
term.log(words)   # ["apple", "fig", "pear"]
```

`join` only accepts arrays of strings. Convert other values first:
`[1, 2].map((n) = string(n)).join(",")`.

## Creating arrays from other values

`array(iterable)` builds a new array from anything iterable. This is also the way to copy an
array:

```argon
term.log(array(1 to 3))              # [1, 2, 3]
term.log(array("abc"))               # ["a", "b", "c"]
term.log(array(tuple(1, 2)))         # [1, 2]
term.log(array({a: 1, b: 2}))        # [tuple("a", 1), tuple("b", 2)]

let original = [1, 2]
let copy = array(original)
copy.append(3)
term.log(original, copy)             # [1, 2] [1, 2, 3]
```

## Membership and equality

```argon
term.log(2 in [1, 2, 3], 5 not in [1, 2, 3])   # true true
```

`==` on arrays compares identity, not contents: `[1] == [1]` is `false`.

## Iteration

```argon
for (item in ["a", "b"]) term.log(item)
```

See [`for` loops](../control-flow/for/README.md).

## Next

- [Dictionaries and tuples](../dictionaries-and-tuples/README.md)
