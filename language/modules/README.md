# Modules and `import`

Every `.ar` file is a module. Its top-level variables are the module's exports.

## `import`

The general form is:

```
import "<path>" [as <name>] [expose <name> [as <alias>], ... | expose *]
```

Given a module `geometry.ar`:

```argon
let pi = 3.14159
let area(r) = pi * r * r
```

all of these work:

```argon
import "geometry" as geo                # bind the whole module to geo
term.log(geo.area(2))

import "geometry" expose area, pi       # pull names into the current scope
term.log(area(2), pi)

import "geometry" expose area as circle_area
term.log(circle_area(2))

import "geometry" expose *              # pull in every top-level name
```

- **`as <name>`** binds the module to `<name>` as a dictionary of its top-level variables,
  so `geo.area` and `geo["area"]` both work.
- **`expose`** copies specific top-level names into the current scope. Each one can be
  renamed with `as`. `expose *` copies every top-level name.
- **`import "path"`** on its own runs the module but binds nothing.

`as` and `expose` can be combined: `import "geometry" as geo expose area`.

## How import paths are resolved

The `.ar` extension is optional, and importing a directory loads its `init.ar`. For an
`import "name"` in a file in directory `dir`, Chloride tries, in order:

1. **Relative to the importing file**: `dir/name`, `dir/name.ar`, `dir/name/init.ar`.
2. **`argon_modules` directories**: `argon_modules/name`, `argon_modules/name.ar` and
   `argon_modules/name/init.ar` in `dir`, then in each parent directory up to the
   filesystem root. This is where [Isotope](../../package-manager/README.md) installs
   packages.
3. **The installation**: the [standard library](../../standard-library/README.md)
   (`stdlib/` next to the interpreter), then globally installed packages
   (`argon_modules/` next to the interpreter).

Paths can contain directories (`import "utils/strings"`) and `..`.

Because local files win, **a file named after a standard library module shadows it**. For
example, a `json.ar` next to your program will be imported by `import "json"` instead of
the standard library, and a file named `build.ar` that runs `import "build"` will try to
import itself.

## Modules run once

A module is executed the first time it is imported. Later imports of the same file (from
anywhere in the program) reuse the same module, so its top-level code doesn't run again.

A module that is imported again while it is still being imported (a circular import)
throws an `ImportError`.

## Running code only when executed directly

`program.main` is `true` in the file passed to `argon`, and `false` in imported modules:

```argon
let main() = term.log("running as a program")

if (program.main) main()
```

## The bytecode cache

Chloride compiles each module to bytecode and caches it in an `__arcache__` directory next
to the source file, so it doesn't need to recompile unchanged files. The cache is checked
against the source and rebuilt automatically when the source changes. It's safe to delete,
and you'll usually want to add `__arcache__/` to `.gitignore`.

## The `program` global

Each module has its own `program` dictionary:

| Key | Description |
|---|---|
| `program.file.path` | absolute path of the current module's file |
| `program.file.name` | file name of the current module, e.g. `main.ar` |
| `program.file.directory` | directory containing the current module (with a trailing `/`) |
| `program.main` | `true` if this module is the program that was run |
| `program.origin` | directory the module was imported from (the working directory for the main program) |
| `program.cwd` | the process's working directory |
| `program.exc` | absolute path to the `argon` executable |

A common pattern is finding files that ship alongside a module:

```argon
import "path" as path
let data_file = path.join(program.file.directory, "data.json")
```

## The `platform` global

| Key | Description |
|---|---|
| `platform.os` | `"linux"`, `"darwin"`, `"windows"` or `"freebsd"` |
| `platform.arch` | CPU architecture, e.g. `"x86_64"` or `"arm64"` |
| `platform.lib_prefix` | shared library file name prefix (`"lib"` on Linux and macOS) |
| `platform.lib_ext` | shared library extension (`".so"`, `".dylib"`, `".dll"`) |
| `platform.args` | the command line as an array of strings (see below) |

`platform.args[0]` is the path of the `argon` executable and `platform.args[1]` is the
program name as typed. The program's own arguments start at index 2:

```argon
# argon greet.ar Ada
let args = platform.args[2:]
term.log(`Hello, $(args.length ? args[0] : "stranger")!`)
```

## The `env` global

`env` is a dictionary of the process's environment variables:

```argon
term.log(env.value_get("HOME", "unknown"))
term.log("PATH" in env)
```

## Next

- [Built-in globals reference](../built-ins/README.md)
- [Standard library overview](../../standard-library/README.md)
