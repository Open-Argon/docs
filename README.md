# Argon Documentation

This is the documentation for **Argon**, a dynamically typed scripting language, as implemented
by **Chloride**, its C interpreter (a bytecode compiler and virtual machine). It also covers
**Isotope**, Argon's package manager.

If you already know a scripting language like Python or JavaScript, most of Argon will feel
familiar. The biggest differences are that blocks are opened with `do` and closed by
indentation, functions are declared with `let name(args) = ...`, and numbers are exact
rationals rather than floating point.

```argon
let greet(name) = `Hello, $(name)!`

for (name in ["Ada", "Grace"]) do
    term.log(greet(name))
```

> **Versions:** this documentation describes Chloride `v4.1.0` and Isotope `1.0.0`. If
> you're running an older version, see [Changes in v4.1.0](#changes-in-v410). Chloride is still
> under active development, so if something here disagrees with the interpreter, trust the
> interpreter (and please fix this doc!).

## Start here

- [Installation](getting-started/README.md)
- [Your first program](getting-started/first-program/README.md)
- [Command line usage](tooling/README.md)
- [The REPL](tooling/repl/README.md)

## Language guide

- [Syntax basics](language/syntax/README.md): comments, `do` blocks, indentation
- [Variables, scope and destructuring](language/variables/README.md)
- [Numbers](language/numbers/README.md)
- [Operators](language/operators/README.md)
- [Control flow](language/control-flow/README.md)
  - [`if` / `else`](language/control-flow/if/README.md)
  - [`while` loops](language/control-flow/while/README.md)
  - [`for` loops and iterators](language/control-flow/for/README.md)
  - [`break` and `continue`](language/control-flow/break-continue/README.md)
- [Functions](language/functions/README.md)
- [Strings](language/strings/README.md)
- [Arrays](language/arrays/README.md)
- [Dictionaries and tuples](language/dictionaries-and-tuples/README.md)
- [Buffers](language/buffers/README.md)
- [Classes and objects](language/classes/README.md)
- [Errors and exceptions](language/error-handling/README.md)
- [Modules and `import`](language/modules/README.md)
- [Built-in globals reference](language/built-ins/README.md)

## Standard library

See the [standard library overview](standard-library/README.md) for a summary of every module.

- [`term`](standard-library/term/README.md): printing and input (global, no import needed)
- [`maths`](standard-library/maths/README.md)
- [`random`](standard-library/random/README.md)
- [`path`](standard-library/path/README.md)
- [`file`](standard-library/file/README.md)
- [`json`](standard-library/json/README.md)
- [`date`](standard-library/date/README.md)
- [`time`](standard-library/time/README.md)
- [`regex`](standard-library/regex/README.md)
- [`network`](standard-library/network/README.md): TCP, TLS and HTTP
- [`url`](standard-library/url/README.md)
- [`hex`](standard-library/hex/README.md)
- [`threading`](standard-library/threading/README.md)
- [`subprocess`](standard-library/subprocess/README.md)
- [`archive`](standard-library/archive/README.md)
- [`build`](standard-library/build/README.md): compiling native extensions

## Isotope (package manager)

- [Isotope overview and commands](package-manager/README.md)
- [Using packages](package-manager/using-packages/README.md)
- [Publishing packages](package-manager/publishing/README.md)
- [`argon-package.json` and `iso-lock.json`](package-manager/package-files/README.md)

## Tooling

- [Command line usage](tooling/README.md)
- [The REPL](tooling/repl/README.md)
- [Writing native extensions in C](tooling/native-extensions/README.md)
- [Building Chloride from source](tooling/building-from-source/README.md)

## Changes in v4.1.0

These behaved differently in Chloride `v4.0.3` and earlier:

- **Operator precedence** is now conventional (see [Operators](language/operators/README.md#precedence)).
  In `v4.0.3`, every operator had its own level, so `8 / 2 * 2` was `2`, `2 ^ 3 ^ 2` was
  `64`, `-2 ^ 2` was `4`, and ranges bound tighter than everything (`-2 to 1` meant
  `-(2 to 1)`).
- `//` and `%` round down for integers too (`(-7) // 2` is `-4`); `v4.0.3` rounded toward zero.
- `exponent()` and `^` on custom classes called `__multiply__`.
- Ranges didn't support `in`, and `to` ranges with fractional bounds (`0.5 to 2.5`) left out
  the end.
- `` \` `` and `\$` escapes, and `forever` loops, didn't work.
- Reading an inherited class attribute through a subclass (`Child.attr`) threw.
- Standard library:
  - `random.int(start, end)` returned values up to `start + end`.
  - `File.read()` always reread from the start of the file.
  - `file.stdnull` didn't discard subprocess output.
  - `json.stringify` rejected tuples.
  - `path.common_prefix` doubled the leading `/`.
  - HTTP `status_text` was always empty.
  - `file.FileError` was separate from the global `FileError`.
  - `Date.from_string` parsed local time.
  - Most of the [`date`](standard-library/date/README.md) module's features (durations,
    date arithmetic, ISO parsing, local time) are new.

## For AI coding agents

[`skills/argon`](skills/argon/SKILL.md) is an agent skill that summarises the language, its
pitfalls, the standard library and Isotope, for coding agents writing Argon. To use it with
Claude Code, copy or symlink the `skills/argon` directory into `~/.claude/skills/` (or into a
project's `.claude/skills/`).

## License

Chloride is released under the GNU General Public License v3.0 (or later). The standard
library is released under the GNU LGPL v3.0 (or later). See the project's `LICENSE.txt` and
`LICENSES/` directory for details.
