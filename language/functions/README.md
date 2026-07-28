# Functions

## Declaring a function

Functions are declared with `let`, just like variables — a function is a value:

```
let add(a, b) = do
    return a + b

term.log(add(2, 3))   # 5
```

For a single-expression body, you can skip `do` and `return` entirely:

```
let square(x) = x * x
```

This is the same pattern used for one-line class methods (see
[Classes and objects](../classes/README.md)).

## Anonymous (lambda) functions

Functions don't need a name. An anonymous function is written the same way, just without
an identifier before the parameter list:

```
term.log(()=10)          # a zero-argument anonymous function returning 10
term.log((x)=10)
term.log((x,y)=10)
term.log((x,y,z)=10)

# named versions, for comparison:
term.log(a()=10)
term.log(b(x)=10)
```

(from [`anonymous_function.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/anonymous_function.ar))

Anonymous functions are commonly passed directly as arguments, e.g. to `array.map`:

```
term.log(
  array(range(-1000000, 1000000))
    .map((val) = val * val)
    .filter((val) = val < 10000)
    .sort((a, b) = a < b ? -1 : a > b ? 1 : 0)
)
```

(from [`map_filter_sort.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/map_filter_sort.ar) — this also shows
method chaining on arrays; see [Arrays](../arrays/README.md).)

## Default arguments

```
let round(num, precision=1) = do
    ...
```

(from [`stdlib/maths/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea43maths/init.ar))

## Variadic arguments: `*args` and `**kwargs`

A function can collect any extra positional arguments into an array with `*args`, and any
extra keyword arguments into a dictionary with `**kwargs`:

```
let f(*args, **kwargs) = do
    term.log(args, kwargs)

f("Hello world", *[1, 2, 3], **{x: 1, y: 5, z: 7})
```

(from [`argument_unpacking.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/argument_unpacking.ar)) — note that
`*` and `**` are also used at the **call site** to unpack an existing array/dictionary into
positional/keyword arguments, exactly like the definition-site collectors.

### Mixing required, default, `*args`, and `**kwargs`

```
let f(x, a=null, *args, **kwargs) = do
    return `x=$(x), a=$(a), args=$(args), kwargs=$(kwargs)`

term.log(f(1))
term.log(f(1, 2))
term.log(f(1, 2, 3))
term.log(f(a=2, *["this should go to x"]))
term.log(f(1, a=2, **["this should go to kwargs"]))
term.log(f(1, a=2, **{wow: "this should go to kwargs"}))
term.log(f(a=2, **{x: "this should go to x"}))
```

(from
[`test_mixed_args_defaults_and_unpacking.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/test_mixed_args_defaults_and_unpacking.ar))

This shows that keyword arguments can fill in named parameters like `x` even when passed
via `**{x: ...}`, and that unpacking a plain array with `**` still works, filling in
`kwargs` positionally in the array's order.

## Recursion

Functions can call themselves normally:

```
let factorial(x) = do
    let n = x - 1
    if (n) return x * factorial(n)
    return 1
```

(from [`factorial.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/factorial.ar))

## Closures

Functions capture variables from their enclosing scope:

```
let f() = do
    let x = 10
    let i = 0
    while (i < 100) do
        i = i + 1
        ...

let g() = f()

g()
```

(from
[`loop_continue_and_break_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/loop_continue_and_break_test.ar))

## Functions as methods on built-in types

You can attach a method directly to a built-in type, e.g. `string`:

```
string.cool(self) = do
    term.log(self, self.length)
    return 10

'hello world'.cool()
string.cool('goodbye world')
```

(from [`class_method.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/class_method.ar)) — note the two
equivalent call styles: as an instance method (`'hello world'.cool()`) or as an explicit
call on the type passing `self` (`string.cool('goodbye world')`).

## Next

- [Classes and objects](../classes/README.md)
- [Error handling](../error-handling/README.md)