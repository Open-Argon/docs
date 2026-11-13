# `build`

```argon
import "build" as build
```

Compiles C code into shared libraries for [native extensions](../../tooling/native-extensions/README.md),
using whichever compilers are installed (gcc, clang, MinGW cross-compilers and so on). It's
designed for package build scripts, so a package can compile its native code on the user's
machine at install time.

> **Don't name your script `build.ar`.** Local files take priority over the standard
> library, so `import "build"` inside `build.ar` imports the script itself. `install.ar` or
> `compile.ar` work fine.

## Example

```
import "build" as build

build.Compiler("c")
    .shared()
    .source("src/native/main.c", "src/native/helpers.c")
    .library_if({"linux": ["pthread", "dl"], "darwin": ["pthread"], "windows": []})
    .output("src/native/bin", "mylib")
    .build()
```

On Linux x86_64 this runs something like
`gcc -shared -fPIC src/native/main.c src/native/helpers.c -lpthread -ldl -o src/native/bin/linux/x86_64/libmylib.so`.
Relative paths are relative to the working directory.

If the installation has an `include/` directory next to its `bin/` directory (containing
`Argon.h`), it's added to the include path automatically. System packages install the headers
into the system include directory (e.g. `/usr/include`) instead, where the compiler already
finds them.

## `build.Compiler(language)`

Creates a compiler configuration for `language` (e.g. `"c"`), using the highest-priority
installed compiler for that language. Throws a `RuntimeError` if none is found. Every
configuration method returns the `Compiler`, so calls can be chained.

| Method | Description |
|---|---|
| `shared()` | build a shared library (what native extensions need) |
| `source(*paths)` | add source files |
| `include(*dirs)` | add include directories |
| `define(*defines)` | add preprocessor definitions, e.g. `"DEBUG"` or `"LEVEL=2"` |
| `library(*names)` | link libraries by name (`"m"` becomes `-lm`) |
| `library_path(*dirs)` | add library search directories |
| `link(*items)` | add raw items to the link line, e.g. a static library `"libfoo.a"` |
| `flag(*flags)` | add raw compiler flags |
| `output(directory, name)` | output to `directory/<os>/<arch>/<prefix><name><ext>` |
| `output_exact(path)` | output to exactly `path` |
| `target(os)` / `arch(arch)` | cross-compile for another OS / architecture |
| `prefer(compiler_name)` | prefer a specific compiler, e.g. `"clang"`, if it can build for the target |
| `build()` | run the compiler; returns `true` on success, `false` if no compiler can reach the target |
| `build_for_targets(targets)` | build for each `[os, arch]` pair; returns `{"os/arch": true/false}` |

`include`, `library`, `library_path`, `link` and `flag` each have an `_if` variant
(`include_if`, `library_if`, ...) that takes a dictionary keyed by target OS, for settings
that only apply on some platforms:

```
.flag_if({"linux": ["-fPIC"], "darwin": ["-fPIC"], "windows": []})
```

If the compiler fails, `build()` throws `build.CompilerError`.

### Building for several platforms

`build_for_targets` builds the same configuration for several targets, skipping any that no
installed compiler can reach. Outputs go into separate `<os>/<arch>/` directories:

```
build.Compiler("c")
    .shared()
    .source("src/native/main.c")
    .output("src/native/bin", "mylib")
    .build_for_targets([
        ["linux", "x86_64"],
        ["windows", "x86_64"],
        ["darwin", "arm64"],
    ])
```

## `build.lib_path(directory, name, os=platform.os, arch=platform.arch)`

Returns the path that `output(directory, name)` produces for a platform, which is the path
to load at run time:

```
import "build" expose lib_path

let native = load_native_code(lib_path(program.file.directory + "native/bin", "mylib"))
```

`lib_path` can also be imported on its own with `import "build/lib_path" expose lib_path`.
That avoids detecting compilers, which `import "build"` does when it loads.

## Next

- [Writing native extensions in C](../../tooling/native-extensions/README.md)
- [Standard library overview](../README.md)
