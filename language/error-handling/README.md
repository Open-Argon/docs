# Errors and exceptions

## `throw`

Throw an exception with `throw`. `throw` is a statement, so it can't be used as a function
body expression on its own; use a `do` block:

```argon
let check(n) = do
    if (n < 0) throw ValueError("n must not be negative")
    return n
```

Only exception objects can be thrown. Throwing anything else (like a string) throws a
`TypeError` instead.

## `try` / `catch`

```argon
let f(x) = do
    x["key"] = 1

try do
    f(10)
catch (Exception as e) do
    term.log(type(e).__name__ + ":", e.message)
```

The `catch` clause has three forms:

| Form | Catches |
|---|---|
| `catch (SomeType as e)` | instances of `SomeType` (and subclasses), bound to `e` |
| `catch (SomeType)` | instances of `SomeType`, without binding a name |
| `catch` | any `Exception` |

An exception that doesn't match keeps propagating outwards. Both the `try` and `catch`
bodies can be a single statement instead of a `do` block:

```argon
let parse(text) = do
    try return number(text)
    catch return null

term.log(parse("12"), parse("twelve"))   # 12 null
```

There is no `finally` clause. To run clean-up code either way, catch, clean up and rethrow:

```argon
let resource = {open: true}
try do
    try do
        throw ValueError("oops")
    catch (Exception as e) do
        resource.open = false
        throw e
catch (ValueError as e) term.log("rethrown:", e.message, resource.open)
```

## Exception objects

Every exception has:

| Member | Description |
|---|---|
| `.message` | the message passed to the constructor |
| `.stack_trace` | an array of `tuple(file, line, column, length)`, oldest call first |
| `type(e).__name__` | the exception's type name |

```argon
try do
    [1, 2][10]
catch (IndexError as e) do
    term.log(e.message)                      # index out of range
    let [file, line, column, length] = e.stack_trace[-1]
    term.log(line, column)
```

`term.error(e)` prints an exception in the same format the interpreter uses for uncaught
errors, without stopping the program:

```argon
try throw ValueError("bad input")
catch (Exception as e) term.error(e)
```

## Uncaught exceptions

An exception that is never caught stops the program. The error, a stack trace and the
source line are printed to stderr, and `argon` exits with status `1`:

```
ERROR! ValueError: bad input
  --> /home/me/project/main.ar:3:5
    |
  3 |     throw ValueError("bad input")
    |     ^^^^^
```

## Defining custom exceptions

Custom exceptions are classes that inherit from `Exception` (or one of its subclasses):

```argon
class AppError(Exception) null
class NotFoundError(AppError) null

let find(name) = do
    throw NotFoundError(`no such item: $(name)`)

try do
    find("widget")
catch (AppError as e) do
    term.log(type(e).__name__, e.message)   # NotFoundError no such item: widget
```

Custom exception classes inherit the constructor and the `message` and `stack_trace`
attributes, so they often need no body.

## Built-in exception types

These are all global:

- `BaseException`: the root of the hierarchy
  - `Exception`: the base for ordinary errors. Subclass this.
    - `RuntimeError`
    - `AssignError`
    - `ValueError`
    - `SyntaxError`
    - `ConversionError`
    - `MathsError`
      - `ZeroDivisionError`
    - `NameError`: an undefined variable
      - `AttributeError`: a missing dictionary key or attribute
    - `TypeError`
    - `InternalError`
    - `IndexError`
    - `FileError`: file and path problems (the [`file`](../../standard-library/file/README.md#exceptions) module's errors are subclasses)
      - `PathError`
    - `ImportError`
  - `SignalException`: control-flow signals, **not** caught by a bare `catch`
    - `KeyboardInterrupt`: thrown when the user presses Ctrl+C
    - `StopIteration`: thrown by an iterator's `__next__` to end a `for` loop (see
      [iterators](../control-flow/for/README.md#the-iterator-protocol))

To catch a `SignalException`, name it (or `BaseException`) explicitly:

```argon
try throw StopIteration()
catch (BaseException as e) term.log("caught", type(e).__name__)
```

Standard library modules define their own exception classes, for example
`file.FileNotFoundError`, `regex.RegexError` and `network.SocketError`. These are documented
on each module's page.

## Next

- [Modules and `import`](../modules/README.md)
