# Variables

Declare a variable with `let`:

```
let x = 10
let name = "chloride"
let pi = 3.14159
```

Once declared, you can reassign without `let`:

```
let x = 10
x = 20
x = x + 1
```

Argon also has compound assignment operators:

```
let i = 0
i += 1   # i = i + 1
i -= 1
i *= 2
i /= 2
```

(See [`iteration.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/iteration.ar) in the test suite for `+=` used
in a loop counter.)

## Dynamic typing

Argon is dynamically typed — a variable can hold any type, and the type isn't declared up
front:

```
let value = 10
value = "now I'm a string"
value = [1, 2, 3]
```

## `null`

The absence of a value is `null`:

```
let x = null
```

## Everything is an object

Unlike some older/simpler interpreters, in Chloride **every value is an object** — numbers,
strings, booleans, functions, and `null` all have an underlying type and can have methods
called on them:

```
term.log((5).__string__())
term.log("hi".length)
```

You can find a value's type with the built-in `type()` function:

```
term.log(type(5))       # number
term.log(type("hi"))    # string
term.log(type([1,2]))   # array
```

and check whether something is an instance of a type (or class) with `is_instance`:

```
term.log(is_instance(5, number))       # true
term.log(is_instance("hi", string))    # true
```

## Scope

Variables declared with `let` are scoped to the block/function they're declared in.
Functions form closures over the scope they were defined in — see
[Functions](../functions/README.md) for details, and
[`loop_continue_and_break_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/loop_continue_and_break_test.ar)
for an example of a nested function (`g`) calling an outer function (`f`) that itself
closes over local variables.

## Next

- [Operators](../operators/README.md)
- [Control flow](../control-flow/README.md)