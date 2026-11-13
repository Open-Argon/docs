# Built-in globals reference

These names are available in every module without an `import`.

## Types

Each type can be called to create or convert a value, used with `is_instance`, and
extended with new methods (see [Functions](../functions/README.md#adding-methods-to-built-in-types)).

| Name | Calling it |
|---|---|
| `object` | the base class of every class |
| `type` | `type(value)` returns the value's class |
| `boolean` | `boolean(value)` converts to `true`/`false` using [truthiness](../variables/README.md#truthiness) |
| `number` | `number(value)` converts to a number |
| `string` | `string(value)` converts to a string |
| `array` | `array(iterable)` builds a new array |
| `tuple` | `tuple(a, b, ...)` builds a tuple of its arguments; see also `tuple.of(iterable)` |
| `dictionary` | `dictionary(value)` copies a dictionary or builds one from key/value pairs |
| `buffer` | see [Buffers](../buffers/README.md) |
| `function` | the type of every function |
| `range` | `range(stop)`, `range(start, stop)`, `range(start, stop, step)` |
| `slice` | `slice(start, stop, step)`, usable as an index |

## Functions

| Name | Description |
|---|---|
| `is_instance(value, type)` | whether `value` is an instance of `type` or a subclass |
| `add`, `subtract`, `multiply`, `divide`, `floor_divide`, `modulo`, `exponent` | the arithmetic operators as functions, applied left to right across any number of arguments (see [Operators](../operators/README.md)) |
| `equal`, `not_equal`, `less_than`, `less_than_equal`, `greater_than`, `greater_than_equal` | the comparison operators as functions |
| `load_native_code(path)` | load a compiled native extension (see [Native extensions](../../tooling/native-extensions/README.md)) |

## Objects

| Name | Description |
|---|---|
| `term` | terminal output and input: [`term.log`, `term.error`, `term.input`](../../standard-library/term/README.md) |
| `program` | information about the current module (see [Modules](../modules/README.md#the-program-global)) |
| `platform` | OS, architecture and command line arguments (see [Modules](../modules/README.md#the-platform-global)) |
| `env` | environment variables as a dictionary (see [Modules](../modules/README.md#the-env-global)) |
| `global` | the global scope as a dictionary |

## Exceptions

`BaseException`, `Exception`, `RuntimeError`, `AssignError`, `ValueError`, `SyntaxError`,
`ConversionError`, `MathsError`, `ZeroDivisionError`, `NameError`, `AttributeError`,
`TypeError`, `InternalError`, `IndexError`, `PathError`, `FileError`, `ImportError`,
`SignalException`, `KeyboardInterrupt` and `StopIteration`. See
[Errors and exceptions](../error-handling/README.md#built-in-exception-types) for the
hierarchy.

## Next

- [Standard library overview](../../standard-library/README.md)
