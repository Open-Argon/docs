# Modules and `import`

## Importing another file

```
import "import_test.ar" expose something

term.log(program)
let instance = something(0)
```

(from [`import.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/import.ar), importing
[`import_test.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/import_test.ar) which defines a class called
`something`)

The general form is:

```
import "<path>" [as <name>] [expose <name1> <name2> ...] | [expose *]
```

- **`import "path"`** on its own runs the file and gives access to it via the path, but
  doesn't bring any of its top-level names into your scope directly.
- **`as <name>`** binds the entire imported module (as a namespace-like object) to
  `<name>`:

  ```
  import "../maths" as maths
  import "path" as path

  let __rand__ = load_native_code(path.resolve(program.file.directory, "native", "bin", "random" + platform.lib_ext))
  ```

  (from [`stdlib/random/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea43random/init.ar)) — after this,
  `maths.int(...)` and `path.resolve(...)` refer to functions defined in those modules.

- **`expose name1 name2 ...`** pulls specific top-level names out of the imported file
  directly into your current scope, so you can use them unqualified:

  ```
  import "random" expose random
  ```

  (from [`array_pop.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/array_pop.ar)) — after this, `random` (an
  instance of the `Random` class defined inside that module) is usable directly.

- **`expose *`** exposes every top-level name from the imported file.

## Paths

Import paths are relative to the importing file. `.ar` files can import both other user
files (e.g. `"import_test.ar"`) and standard library modules by relative path (e.g.
`"random"`, which imports a directory containing an `init.ar`).

## Circular imports

Chloride detects circular imports and raises an `ImportError` rather than recursing
forever (see `import.c`'s `importing_hash_table` check, which is used to detect a file
that is already in the process of being imported).

## The `program` global

Inside any running Argon file, a `program` dictionary is available describing the running
program:

| Key | Description |
|---|---|
| `program.file.path` | full path to the current file |
| `program.file.name` | basename of the current file |
| `program.file.directory` | directory containing the current file |
| `program.main` | `true` if this file is the one that was run directly (not imported) |
| `program.origin` | the current working directory at import time |
| `program.cwd` | the process's working directory |
| `program.exc` | path to the interpreter executable |

```
let __rand__ = load_native_code(
    path.resolve(program.file.directory, "native", "bin", "random" + platform.lib_ext)
)
```

(from [`stdlib/random/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea43random/init.ar), using
`program.file.directory` to locate a native shared library relative to the module's own
location)

## The `platform` global

| Key | Description |
|---|---|
| `platform.os` | e.g. `"windows"`, `"linux"`, `"darwin"` |
| `platform.lib_prefix` | native library filename prefix for this platform |
| `platform.lib_ext` | native library file extension for this platform (e.g. `.so`, `.dll`) |
| `platform.args` | array of command-line arguments the interpreter was invoked with |

## The `env` global

`env` is a dictionary of the process's environment variables:

```
let home = env["HOME"]
```

(pattern seen in [`stdlib/path/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea43path/init.ar)'s
`expand_user` function, which reads `env["HOME"]` or `env["USERPROFILE"]`)

## Loading native code

`load_native_code(path)` loads a compiled native shared library (used internally by
standard library modules like `random`, `file`, `network`, `threading`, `date`, and `time`,
which are backed by C implementations for performance/OS access). Most user code won't
need this directly unless writing your own native extension.

## Next

- [Standard library overview](../../README.md#standard-library)