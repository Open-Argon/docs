# Variables, scope and destructuring

## Declaring variables

Declare a variable with `let`:

```argon
let x = 10
let name = "chloride"
let pi = 3.14159
```

A declaration without a value initialises the variable to `null`:

```argon
let result
term.log(result)   # null
```

Several variables can be declared in one statement, separated by commas:

```argon
let a = 1, b = 2
let first, second = "two"   # first is null, second is "two"
term.log(a, b, first, second)
```

Declaring the same name twice in the same scope is an error:

```
let x = 1
let x = 2   # RuntimeError: Identifier 'x' has already been declared in the current scope
```

## Assignment

Once declared, assign a new value with `=` (no `let`):

```argon
let x = 10
x = 20
x = x + 1
```

Compound assignment operators are available for every arithmetic operator:

```argon
let i = 10
i += 1    # i = i + 1
i -= 1
i *= 2
i /= 4
i //= 2
i %= 3
i ^= 2    # exponent
term.log(i)
```

Assignment is an expression, so it returns the assigned value:

```argon
let x = 0
term.log(x = 5)   # 5
```

Assigning to a name that hasn't been declared anywhere creates it in the **current**
scope, as if it had been declared with `let`. Prefer `let`, which makes the scope explicit.

## Dynamic typing

Argon is dynamically typed. A variable can hold any type, and the type isn't declared up
front:

```argon
let value = 10
value = "now I'm a string"
value = [1, 2, 3]
```

## `null`, `true` and `false`

The absence of a value is `null`. The booleans are `true` and `false`.

```argon
let nothing = null
let yes = true
term.log(nothing, yes, !yes)   # null true false
```

## Everything is an object

In Chloride **every value is an object**: numbers, strings, booleans, functions, classes
and `null` all have a type, and that type can have methods.

`type(value)` returns the value's type object. Use its `__name__` to get the name as a
string:

```argon
term.log(type(5).__name__)        # number
term.log(type("hi").__name__)     # string
term.log(type([1, 2]).__name__)   # array
term.log(type(null).__name__)     # null_type
term.log(type(5) == number)       # true
```

`is_instance(value, type)` checks whether a value is an instance of a type or one of its
subclasses:

```argon
term.log(is_instance(5, number))      # true
term.log(is_instance("hi", string))   # true
term.log(is_instance("hi", number))   # false
```

## Truthiness

Any value can be used as a condition. The following are **falsy**:

- `false`
- `null`
- `0`
- `""` (the empty string)

Everything else is truthy, **including empty arrays and dictionaries**:

```argon
term.log(boolean(0), boolean(""), boolean(null))   # false false false
term.log(boolean([]), boolean({}), boolean("0"))   # true true true
```

To check whether an array is empty, test its length: `if (items.length) ...`. A class can
define its own truthiness with a `__boolean__` method (see
[Classes](../classes/README.md#operator-overloading-dunder-methods)).

## Scope

Every `do` block creates a new scope. A variable declared with `let` inside a block is only
visible inside that block (and blocks nested within it):

```argon
if (true) do
    let inner = 1
    term.log(inner)   # 1

# term.log(inner) here would throw NameError: Identifier 'inner' is not defined
```

Assigning to a variable from an outer scope updates the outer variable:

```argon
let count = 0
let increment() = do
    count += 1

increment()
increment()
term.log(count)   # 2
```

Functions are closures: they keep access to the variables in scope where they were
defined, even after that scope has finished:

```argon
let make_counter() = do
    let n = 0
    return () = do
        n += 1
        return n

let counter = make_counter()
counter()
term.log(counter())   # 2
```

## `delete`

`delete` removes a variable from its scope. It also removes dictionary entries, array
items and object attributes (see [Arrays](../arrays/README.md) and
[Dictionaries](../dictionaries-and-tuples/README.md)):

```argon
let temp = 1
delete temp
# using temp now throws NameError
```

## Destructuring

A `let` declaration can unpack arrays, tuples and dictionaries.

Arrays and tuples are unpacked by position. `*name` collects the remaining items into an
array, and patterns can be nested:

```argon
let [a, b] = [1, 2]
let [first, *rest] = [1, 2, 3, 4]
let [x, [y, z]] = [1, [2, 3]]
term.log(a, b, first, rest, x, y, z)   # 1 2 1 [2, 3, 4] 1 2 3
```

Dictionaries are unpacked by key. `{name}` is shorthand for `{name: name}`, and
`{key: pattern}` binds the value at `key` to a different name or pattern:

```argon
let {name, version} = {name: "argon", version: 4}
let {name: project_name} = {name: "chloride"}
term.log(name, version, project_name)   # argon 4 chloride
```

The same patterns work in [`for` loops](../control-flow/for/README.md):

```argon
for ([key, value] in {a: 1, b: 2}) term.log(key, value)
```

## Next

- [Numbers](../numbers/README.md)
- [Operators](../operators/README.md)
