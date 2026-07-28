# Classes and objects

Chloride has "a proper class and inheritance system" (per the project README) — classes
are real objects, supporting inheritance and introspection.

## Defining a class

```
class name do
    this.__init__(self) = do
        term.log("hello world")

term.log(name)      # the class itself
term.log(name())    # calling it constructs an instance; prints "hello world"
```

(from [`class_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/class_test.ar))

Inside a class body, `this.method_name(self, ...) = do ... ` defines an instance method.
The first parameter is conventionally named `self` and refers to the instance, similar to
Python.

## Inheritance

Pass the parent class in parentheses after the class name:

```
class test(name) do
    this.wow(self) = do
        term.log(self)

test().wow()
```

(from [`class_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/class_test.ar)) — `test` inherits from
`name`.

## Public vs. private members

Members defined with `this.member = ...` are **public** — accessible from outside the
class. Members defined with a plain `let` inside the class body are **private** — only
accessible from within the class's own methods:

```
class some_public_private_statics do
    this.some_public = "hello im public"
    let some_private = "hello im private"

    this.get_the_private(self) = do
        return some_private

term.log(some_public_private_statics.some_public)   # OK — public
term.log(some_public_private_statics.some_private)  # errors — private, not accessible
```

(from [`class_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/class_test.ar), which explicitly comments
that accessing the private member from outside `# will error`)

## Static vs. instance members

`this.some_public` in the example above is a **static/class-level** member (accessed on
the class itself, `some_public_private_statics.some_public`, without needing an instance).
Instance data is normally set inside `__init__` via `self.attr = value`:

```
class custom_range do
    this.__init__(self, stop) = do
        self.stop = stop
    this.__iter__(self) = custom_range_iterator(self.stop)
```

(from [`custom_range.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/custom_range.ar))

## The constructor: `__init__`

`__init__` runs when the class is called like a function:

```
class something do
    this.__init__(self, x) = do
        self.x = x
    this.f(self) = do
        term.log(self, self.x, self.x = self.x + 1)

let instance = something(0)
instance.f()   # logs the instance, its x, and increments x
```

(from [`import_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/import_test.ar), showing that an
assignment like `self.x = self.x + 1` is itself an expression that returns the assigned
value — this is why it can appear as a `term.log(...)` argument.)

## Operator overloading (dunder methods)

Classes can customise how they behave with built-in operators and syntax by implementing
"dunder" (double-underscore) methods. These are exactly how the built-in types (`number`,
`string`, `array`, etc.) implement their own behaviour, so you can make your own types work
just as naturally.

| Method | Triggered by |
|---|---|
| `__init__(self, ...)` | calling the class, e.g. `MyClass(args)` |
| `__string__(self)` | converting to a string / `term.log` |
| `__getitem__(self, key)` | `obj[key]` |
| `__setitem__(self, key, value)` | `obj[key] = value` |
| `__contains__(self, item)` | `item in obj` |
| `__iter__(self)` | starting a `for` loop over `obj` |
| `__next__(self)` | each step of a `for` loop, on an iterator object |
| `__equal__` / `__not_equal__` | `==` / `!=` |
| `__less_than__` / `__less_than_equal__` / `__greater_than__` / `__greater_than_equal__` | `<`, `<=`, `>`, `>=` |
| `__add__` / `__subtract__` / `__multiply__` / `__division__` / `__floor_division__` / `__modulo__` / `__exponent__` | `+`, `-`, `*`, `/`, `//`, `%`, `**` |
| `__negation__` | unary `-` |
| `__hash__` | using the object as a dictionary key |
| `__name__` | a class-level display name (used by exception types, for example) |

### Example: custom subscript / slice handling

```
class x do
    this.__getitem__(self, key) = key
    this.__setitem__(self, key, value) = key

term.log(x()[1:2, 2])
term.log(x()[1:2, 2] = 1)
```

(from [`slice.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/slice.ar))

### Example: making a class iterable

```
class custom_range_iterator do
    this.__init__(self, stop) = do
        self.current = 0
        self.stop = stop
    this.__next__(self) = do
        if (self.stop <= self.current) do
            throw StopIteration()
        let result = self.current
        self.current = self.current + 1
        return result

class custom_range do
    this.__init__(self, stop) = do
        self.stop = stop
    this.__iter__(self) = custom_range_iterator(self.stop)
```

(from [`custom_range.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/custom_range.ar)) — see
[`for` loops and iterators](../control-flow/for/README.md) for how this plugs into `for`.

## Introspection

- `type(obj)` returns an object's class/type.
- `is_instance(obj, SomeType)` checks whether `obj` is an instance of `SomeType` (or a
  subclass).

```
term.log(type(5))                 # number
term.log(is_instance(5, number))  # true
```

You can also assign to class-level attributes dynamically through `type()`:

```
term.log(type(some_public_private_statics_instance).some_public = 10)
```

(from [`class_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/class_test.ar))

## Next

- [Errors and exceptions](../error-handling/README.md)