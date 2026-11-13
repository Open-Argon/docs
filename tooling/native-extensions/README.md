# Writing native extensions in C

Native extensions are shared libraries (`.so`, `.dylib` or `.dll`) that add functions
written in C (or anything that can export a C ABI) to Argon. The standard library uses them
for everything that needs the operating system, like files, sockets and threads.

## A minimal extension

`native/greet.c`:

```c
#include <Argon.h>
#include <ArgonFunction.h>

ARGON_FUNCTION(add, {
  if (api->fix_to_arg_size(2, argc, err))
    return api->ARGON_NULL;

  int64_t a = api->argon_to_i64(argv[0], err);
  if (api->is_error(err))
    return api->ARGON_NULL;

  int64_t b = api->argon_to_i64(argv[1], err);
  if (api->is_error(err))
    return api->ARGON_NULL;

  return api->i64_to_argon(a + b);
})

ARGON_FUNCTION(fail, {
  return api->throw_argon_error(err, api->RuntimeError, "bad value: %d", 7);
})

INIT_ARGON_MODULE(
  REGISTER_ARGON_FUNCTION(add)
  REGISTER_ARGON_FUNCTION(fail)
  api->register_ArgonObject(reg, "version",
                            api->string_to_argon((struct string){"1.0", 3}));
)
```

Compile it with the [`build`](../../standard-library/build/README.md) module, from a script
**not** named `build.ar`, e.g. `compile.ar`:

```
import "build" as build

build.Compiler("c")
    .shared()
    .source("native/greet.c")
    .output("native/bin", "greet")
    .build()
```

```bash
argon compile.ar
# [build] /usr/bin/gcc -shared -fPIC native/greet.c -o native/bin/linux/x86_64/libgreet.so
```

Then load it with `load_native_code`:

```
import "build/lib_path" expose lib_path

let greet = load_native_code(lib_path(program.file.directory + "native/bin", "greet"))

term.log(greet.add(2, 3), greet.version)   # 5 1.0

try greet.fail()
catch (RuntimeError as e) term.log(e.message)   # bad value: 7
```

`load_native_code(path)` returns a dictionary of everything the library registered. Wrap it
in an Argon module to give it a friendlier interface. Most standard library modules are a
small `init.ar` doing exactly that.

## How it works

### The entry point

The library must export `argon_module_init`, which Chloride calls once when it is loaded.
`INIT_ARGON_MODULE(...)` defines it:

```c
void argon_module_init(ArgonState *vm, ArgonNativeAPI *api, ArgonError *err,
                       ArgonObjectRegister *reg);
```

Add values to the returned dictionary with `api->register_ArgonObject(reg, name, object)`.
`REGISTER_ARGON_FUNCTION(name)` registers a function defined with `ARGON_FUNCTION(name, ...)`.

### Native functions

`ARGON_FUNCTION(name, { ... })` defines a function with these parameters:

| Parameter | Description |
|---|---|
| `size_t argc` | number of positional arguments |
| `ArgonObject **argv` | the positional arguments |
| `ArgonHashmap *kwargs` | keyword arguments, or `NULL` (read with `api->get_from_hashmap_string_key(kwargs, "name")`) |
| `ArgonError *err` | where to report an error |
| `ArgonState *state` | the interpreter state, needed to call back into Argon |
| `ArgonNativeAPI *api` | the API function table |

A native function must return an `ArgonObject *`; return `api->ARGON_NULL` for no value.
When registered as a method on a class, `argv[0]` is the instance.

### Errors

Conversion functions such as `argon_to_i64` report problems through `err`. Check
`api->is_error(err)` after each one and return immediately if it is set.

To throw an exception, return `api->throw_argon_error(err, type, format, ...)`, where `type` is
an exception class such as `api->RuntimeError`, `api->TypeError` or `api->IndexError`, and
`format` is a `printf`-style message. You can also pass an exception class from Argon code
as an argument and throw that; this is how standard library modules throw their own
exception types.

### Converting values

| From Argon | To Argon |
|---|---|
| `argon_to_i64(obj, err)` | `i64_to_argon(int64_t)` |
| `argon_to_double(obj, err)` | `double_to_argon(double)` |
| `argon_to_rational(obj, err)` (returns `struct rational {n, d}`) | `rational_to_argon(struct rational)` |
| `argon_to_string(obj, err)` (returns `struct string {data, length}`) | `string_to_argon(struct string)` |
| `argon_buffer_to_buffer(obj, err)` (returns `struct buffer {data, size}`) | `create_argon_buffer(size)` |
| `argon_to_array(obj, err)` (returns `struct array {items, size}`) | `create_argon_array(items, size)` |
| | `hashmap_to_dictionary(create_hashmap())` with `add_to_hashmap_string_key` |

`api->ARGON_NULL`, `api->ARGON_TRUE` and `api->ARGON_FALSE` are the `null` and boolean
values. Strings are not null-terminated: always use `length`.

Other useful members of the API include `call` (call an Argon function), `malloc`/`free`
(memory managed by the interpreter's allocator), and `register_thread`/`unregister_thread`
(for native threads that call into Argon). See `Argon.h` for the full list.

## Headers

The headers `Argon.h`, `ArgonFunction.h` and `ArgonTypes.h` are installed with Chloride (in
`/usr/include` for the Linux packages, or the `include/` directory of the installation). The
headers are MIT licensed. The `native/` directories of the standard library modules in the
Chloride repository have more examples.

## Distributing native extensions

To publish an extension as an Isotope package, include the C sources and a build script
that compiles them at install time. See
[Publishing packages: packages with native code](../../package-manager/publishing/README.md#packages-with-native-code).
