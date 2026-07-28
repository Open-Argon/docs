# Errors and exceptions

## `throw`

Raise an exception with `throw`:

```
let f() = do
    throw MyError("something went wrong")

f()
```

## `try` / `catch`

```
let f(x) = do
    x["hello"] = 1

try do
    f(10)
catch (Exception as e) do
    term.log(type(e).__name__ + ":", e.message)
    term.log("trace:", e.stack_trace)
```

(from [`exception_catch.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/exception_catch.ar))

Every caught exception object exposes at least:

- `.message` — the error message.
- `.stack_trace` — a stack trace for the error.
- Its class has a `.__name__` — the exception type's display name.

A bare `catch do ... ` (without `(Exception as e)`) also works when you don't need the
exception value:

```
let is_relative_to(p, base) = do
    try do
        relative_to(p, base)
        return true
    catch do
        return false
```

(from [`stdlib/path/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/path/init.ar))

## Defining custom exceptions

Custom exceptions are just classes that inherit (directly or indirectly) from the built-in
`Exception` type:

```
class CustomError(Exception) do

class MyError(CustomError) do

let f() = do
    throw MyError(`hel\u1324lo`)

f()
```

(from [`error_system.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/error_system.ar)) — note that exception
classes can have empty bodies; they inherit `__init__`, `.message`, `.stack_trace`, etc.
from `Exception`.

## Built-in exception types

The global scope defines a hierarchy of built-in exceptions you can catch or subclass:

- `BaseException` — the root of the hierarchy.
  - `Exception` — the general-purpose base most user code should subclass.
    - `RuntimeError`
    - `AssignError`
    - `ValueError`
    - `SyntaxError`
    - `ConversionError`
    - `MathsError`
    - `ZeroDivisionError`
    - `NameError`
    - `TypeError`
    - `InternalError`
    - `IndexError`
    - `AttributeError`
    - `PathError`
    - `FileError`
    - `ImportError`
  - `SignalException`
    - `KeyboardInterrupt`
  - `StopIteration` — thrown by an iterator's `__next__` to signal the end of iteration
    (see [`for` loops and iterators](../control-flow/for/README.md)).

Module-specific standard library packages (like `file` and `regex`) also define their own
exception subclasses in an `exceptions.ar` file — see, for example,
[`stdlib/file/exceptions.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/file/exceptions.ar) and
[`stdlib/regex/exceptions.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/regex/exceptions.ar).

## Next

- [Modules and `import`](../modules/README.md)