# Functions

## Declaring a function

Functions are declared with `let`, just like variables, because a function is a value:

```argon
let add(a, b) = do
    return a + b

term.log(add(2, 3))   # 5
```

For a single-expression body, skip `do` and `return`. The expression is the return value:

```argon
let square(x) = x * x
term.log(square(4))   # 16
```

A function that finishes without `return` (or with a bare `return`) returns `null`.

## Anonymous functions

Leave out the name to create an anonymous function (a lambda):

```argon
let double = (x) = x * 2
let greet = () = "hello"
let add = (a, b) = do
    return a + b

term.log(double(4), greet(), add(1, 2))   # 8 hello 3
```

Anonymous functions are commonly passed straight to other functions:

```argon
term.log([3, 1, 2].sort((a, b) = a - b))   # [1, 2, 3]
term.log(array(1 to 10)
    .map((n) = n * n)
    .filter((n) = n % 2 == 0))             # [4, 16, 36, 64, 100]
```

When an anonymous function with a `do` body is an argument, the closing `)` goes on a new
line at the indentation of the call:

```argon
let run(callback) = callback()

term.log(run(() = do
    let x = 20
    return x + 1
))   # 21
```

## Parameters

### Default values

```argon
let greet(name, greeting = "Hello") = `$(greeting), $(name)!`

term.log(greet("Ada"))                   # Hello, Ada!
term.log(greet("Ada", "Hi"))             # Hi, Ada!
term.log(greet("Ada", greeting = "Yo"))  # Yo, Ada!
```

Parameters without defaults must come before parameters with defaults.

Default values are evaluated **once, when the function is defined**, and the same value is
reused for every call. Avoid mutable defaults like `[]` or `{}`, because changes to them
persist between calls. Default to `null` and create the value inside the function instead:

```argon
let add_item(item, items = null) = do
    if (items == null) items = []
    items.append(item)
    return items

add_item(1)
term.log(add_item(2))   # [2]
```

### Optional parameters: `name?`

`name?` is shorthand for a parameter that defaults to `null`:

```argon
let describe(value, label?) = do
    if (label) return `$(label): $(value)`
    return string(value)

term.log(describe(5), describe(5, "count"))   # 5 count: 5
```

### Keyword arguments

Any named parameter can be passed by name:

```argon
let box(width, height = 1) = width * height
term.log(box(height = 3, width = 2))   # 6
```

### Variadic parameters: `*args` and `**kwargs`

`*name` collects extra positional arguments into a **tuple**, and `**name` collects extra
keyword arguments into a **dictionary**. A function can have at most one of each, and they
must come last:

```argon
let f(x, a = null, *args, **kwargs) = `x=$(x), a=$(a), args=$(args), kwargs=$(kwargs)`

term.log(f(1))                  # x=1, a=null, args=tuple(), kwargs={}
term.log(f(1, 2, 3, 4, k = 5))  # x=1, a=2, args=tuple(3, 4), kwargs={"k": 5}
```

### Unpacking arguments at the call site

`*iterable` spreads an array or tuple into positional arguments, and `**dictionary` spreads
a dictionary into keyword arguments:

```argon
let f(x, y, z) = x + y + z
term.log(f(*[1, 2, 3]))              # 6
term.log(f(1, **{y: 2, z: 3}))       # 6
```

## Returning multiple values

Return an array, tuple or dictionary and [destructure](../variables/README.md#destructuring)
it:

```argon
let min_max(items) = do
    let sorted = array(items).sort((a, b) = a - b)
    return [sorted[0], sorted[-1]]

let [low, high] = min_max([5, 2, 9])
term.log(low, high)   # 2 9
```

## Recursion

```argon
let fib(n) = n < 2 ? n : fib(n - 1) + fib(n - 2)
term.log(fib(10))   # 55
```

## Closures

Functions capture the variables in scope where they are defined and can update them:

```argon
let make_counter() = do
    let count = 0
    return () = do
        count += 1
        return count

let counter = make_counter()
counter()
counter()
term.log(counter())   # 3
```

## Functions are values

Functions can be stored in variables, arrays and dictionaries, passed as arguments, and
returned from other functions:

```argon
let ops = {
    add: (a, b) = a + b,
    mul: (a, b) = a * b,
}
term.log(ops.add(2, 3), ops["mul"](2, 3))   # 5 6
```

`term.log`ging a function shows its name, e.g. `<function add at 0x...>`.

## Adding methods to built-in types

You can attach a method to a built-in type, and every value of that type gains it:

```argon
string.shout(self) = self.upper() + "!"

term.log("hello".shout())        # HELLO!
term.log(string.shout("hey"))    # HEY!
```

Note the two equivalent call styles: as a method on a value, or called on the type with the
value passed as `self`. See [Classes](../classes/README.md) for more on methods.

## Next

- [Strings](../strings/README.md)
- [Classes and objects](../classes/README.md)
