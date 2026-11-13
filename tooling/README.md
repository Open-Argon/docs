# Command line usage

## Running a program

Pass a file name to `argon`:

```bash
argon my_program.ar
```

The `.ar` extension can be omitted, and a directory containing an `init.ar` can be run
directly:

```bash
argon my_program        # runs my_program.ar
argon my_project        # runs my_project/init.ar
```

The name is resolved the same way as an [`import`](../language/modules/README.md#how-import-paths-are-resolved),
so if no matching file exists in the current directory, `argon` also looks in
`argon_modules` directories and in the installation. That's how `argon isotope` runs the
package manager.

## Passing arguments

Everything after the program name is available in `platform.args`. The first two entries
are the interpreter and the program, so your arguments start at index 2:

```bash
argon greet.ar Ada Grace
```

```argon
let names = platform.args[2:]
for (name in names) term.log(`Hello, $(name)!`)
```

## Reading a program from standard input

When standard input isn't a terminal, `argon` with no arguments runs whatever is piped into
it:

```bash
echo 'term.log("hello from a pipe")' | argon
```

## Exit status and errors

`argon` exits with status `0` when the program finishes, and `1` when it stops because of an
uncaught exception (or the program file can't be found). The error is printed to standard
error:

```
ERROR! FileError: Unable to find file 'missing.ar'
```

Pressing Ctrl+C throws a `KeyboardInterrupt` exception in the running program.

## Checking the version

```bash
argon --version
```

prints the Chloride version, for example `v4.1.0`.

## The REPL

Running `argon` with no arguments in a terminal starts the interactive REPL. See
[the REPL](repl/README.md).

## The bytecode cache

When a file is run or imported, Chloride stores its compiled bytecode in an `__arcache__`
directory next to it, to start faster next time. See
[Modules: the bytecode cache](../language/modules/README.md#the-bytecode-cache).

## Isotope

The package manager has its own command, `isotope`. See [Isotope](../package-manager/README.md).
