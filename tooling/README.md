# Command line usage

Chloride provides a command line interface for running Argon programs and accessing development tools.

## Running a program

An Argon source file can be run by passing its filename to `argon`:

```
argon my_program.ar
```

If the file uses the `.ar` extension, the extension can be omitted:

```
argon my_program
```

Both commands will run the same file:

```
my_program.ar
```

## Checking the version

To display the installed Chloride version, run:

```
argon --version
```

This prints version information for the current installation.

## REPL

Chloride also includes an interactive REPL for running Argon code interactively.

See the [REPL documentation](repl/README.md) for more information.