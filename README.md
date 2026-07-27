# Chloride / Argon Documentation

This is community documentation for **Argon**, the programming language, as implemented by
**Chloride**, its C-based interpreter (a bytecode compiler + virtual machine).

If you already know a scripting language like Python or JavaScript, most of this will feel
familiar. Argon uses `do` blocks instead of `{ }`, and functions are declared as
`let name(args) = do ... `.

> **Note:** This documentation is written from reading the interpreter's source code and
> test suite, since the project doesn't yet have complete official docs. If something here
> looks wrong, trust the source code (and please fix this doc!).

## Start here

- [Installation](getting-started/README.md)
- [Your first program](getting-started/first-program/README.md)
- [Running the REPL](tooling/repl/README.md)

## Language guide

- [Variables and `let`](language/variables/README.md)
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
- [Classes and objects](language/classes/README.md)
- [Errors and exceptions](language/error-handling/README.md)
- [Modules and `import`](language/modules/README.md)

## Standard library

- [`term`](standard-library/term/README.md) — printing and input
- [`maths`](standard-library/maths/README.md)
- [`random`](standard-library/random/README.md)
- [`path`](standard-library/path/README.md)
- [`json`](standard-library/json/README.md)
- [`file`](standard-library/file/README.md)
- [`date`](standard-library/date/README.md)
- [`time`](standard-library/time/README.md)
- [`network`](standard-library/network/README.md)
- [`threading`](standard-library/threading/README.md)
- [`regex`](standard-library/regex/README.md)

## Tooling

- [Command line usage](tooling/README.md)
- [The REPL](tooling/repl/README.md)
- [Building from source](tooling/building-from-source/README.md)

## License

Chloride is released under the GNU General Public License v3.0 (or later). See the
project's `LICENSE` file for details.