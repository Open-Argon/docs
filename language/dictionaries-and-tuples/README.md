# Dictionaries and tuples

## Dictionaries

Dictionaries are written with `{ key: value, ... }`. Keys don't need to be quoted
identifiers:

```
let d = {x: 1, y: 5, z: 7}
term.log(d["x"])
```

This is used, for example, when unpacking keyword arguments in a function call:

```
f("Hello world", *[1,2,3], **{x:1, y:5, z:7})
```

(from [`argument_unpacking.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/argument_unpacking.ar))

### Indexing

```
d["key"] = value      # set
term.log(d["key"])    # get
```

This is backed by the `__getitem__` / `__setitem__` dunder methods, same as arrays.

### Membership: `in`

```
term.log("key" in d)
```

Checks whether `d` has the given key (backed by `__contains__`).

### Iterating a dictionary

Iterating a dictionary yields `[key, value]` pairs:

```
for (keyval in value) do
    let key = keyval[0]
    let val = keyval[1]
    ...
```

(adapted from [`stdlib/json/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/json/init.ar)'s `stringify`
function, which walks a dictionary's entries this way to build JSON output)

### Converting to/from a dictionary

The built-in `dictionary` type can be used with `is_instance` to check if a value is a
dictionary:

```
if (is_instance(value, dictionary)) do
    ...
```

## Tuples

Tuples are fixed-size, ordered collections, typically returned from functions that need to
return more than one value (Argon has no native multiple-return-value syntax, so a tuple
fills that role). They're constructed with the `tuple(...)` built-in:

```
let split(p) = do
    ...
    if (idx == -1) do
        return tuple("", p)
    ...
    return tuple(head, tail)
```

(from [`stdlib/path/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/path/init.ar)'s `split` function,
which returns a `tuple(head, tail)`)

### Indexing a tuple

Tuples support the same `[index]` and `.length` as arrays:

```
let result = split("/a/b/c")
term.log(result[0])   # head
term.log(result[1])   # tail
```

### Iterating a tuple

Like arrays, tuples are iterable with `for` and support `__iter__`/`__next__`.

## Next

- [Classes and objects](../classes/README.md)