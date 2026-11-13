# Dictionaries and tuples

## Dictionaries

Dictionaries map keys to values and keep their insertion order. They're written with
`{key: value, ...}`. A bare identifier key is a string, so `{name: 1}` and `{"name": 1}`
are the same. To use a variable's value or any other expression as a key, wrap it in
parentheses:

```argon
let key = "dynamic"
let d = {
    name: "argon",
    "with space": 1,
    3: "three",
    (key): true,
}
term.log(d)   # {"name": "argon", "with space": 1, 3: "three", "dynamic": true}
```

`{name}` is shorthand for `{name: name}`:

```argon
let name = "chloride"
let version = 4
term.log({name, version})   # {"name": "chloride", "version": 4}
```

Keys can be any value with a hash: strings, numbers, booleans, `null`, and objects whose
class defines `__hash__`.

### Reading and writing

Values can be read and written with indexing or, for keys that are valid identifiers, with
`.`:

```argon
let d = {name: "argon"}
d["version"] = 4
d.stable = true
term.log(d.name, d["version"], d.stable)   # argon 4 true
```

Reading a missing key throws an `AttributeError`. Use `in` to check first, or
`value_get(key, default?)`, which returns `default` (or `null`) for missing keys:

```argon
let d = {a: 1}
term.log("b" in d)             # false
term.log(d.value_get("b"))     # null
term.log(d.value_get("b", 0))  # 0
```

### Removing entries

```argon
let d = {a: 1, b: 2}
delete d.a
delete d["b"]
term.log(d)   # {}
```

### Iterating

Iterating a dictionary yields a `tuple(key, value)` for each entry, which can be
destructured:

```argon
let scores = {ada: 10, grace: 12}
for ([name, score] in scores) term.log(name, score)
```

Dictionaries don't have a `length` property. Use `array(d).length` to count entries.

### Creating dictionaries

`dictionary(value)` copies a dictionary, or builds one from an iterable of `[key, value]`
pairs:

```argon
term.log(dictionary([["k", "v"], ["x", 1]]))   # {"k": "v", "x": 1}
```

Like arrays, `==` on dictionaries compares identity, not contents.

## Tuples

Tuples are fixed-size, immutable sequences. Create one with `tuple(...)`, passing the items
as arguments, or with `tuple.of(iterable)`:

```argon
let t = tuple(1, 2, 3)
term.log(t)                   # tuple(1, 2, 3)
term.log(tuple.of([4, 5]))    # tuple(4, 5)
```

Note that `tuple([4, 5])` creates a one-item tuple containing the array.

Tuples support `length`, indexing, slicing (which returns a tuple), iteration and
destructuring, but not item assignment:

```argon
let t = tuple("a", "b", "c")
term.log(t.length, t[0], t[-1], t[1:])   # 3 a c tuple("b", "c")
let [first, *rest] = t
term.log(first, rest)                    # a ["b", "c"]
```

The interpreter uses tuples for `*args` (see [Functions](../functions/README.md)), for the
entries yielded by dictionary iteration, and for exception
[stack traces](../error-handling/README.md#exception-objects).

## Next

- [Buffers](../buffers/README.md)
- [Classes and objects](../classes/README.md)
