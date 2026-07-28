# `path`

```
import "../stdlib/path" as path
```

A pure-Argon path manipulation library (see
[`stdlib/path/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/path/init.ar)), similar in spirit to
Python's `pathlib`/`os.path`. Paths are always normalised internally to use `/` as the
separator, even on Windows.

## Reference

| Function | Description |
|---|---|
| `path.sep` | the path separator (`"/"`) |
| `path.os` | the current `platform.os` value |
| `path.normalise(p)` | normalise a path's separators and remove trailing slashes |
| `path.join(*parts)` | join path components together |
| `path.split(p)` | returns `tuple(head, tail)` |
| `path.dirname(p)` | the directory portion of a path |
| `path.basename(p)` | the final component of a path |
| `path.splitext(p)` | returns `tuple(root, ext)` |
| `path.ext(p)` | just the extension, e.g. `.txt` |
| `path.stem(p)` | the basename without its extension |
| `path.parts(p)` | an array of path components |
| `path.is_absolute(p)` / `path.is_relative(p)` | check whether a path is absolute |
| `path.resolve(*parts)` | lexically resolve `.` and `..` (no filesystem access) |
| `path.relative_to(p, base)` | express `p` relative to `base` |
| `path.is_relative_to(p, base)` | whether `p` can be expressed relative to `base` |
| `path.with_name(p, name)` | replace the final path component |
| `path.with_stem(p, new_stem)` | replace the stem, keeping the extension |
| `path.with_ext(p, new_ext)` | replace the extension |
| `path.common_prefix(paths)` | the common leading path shared by all `paths` |
| `path.expand_user(p)` | expand a leading `~` to the user's home directory |
| `path.is_hidden(p)` | whether the basename starts with `.` |

## Examples

```
term.log(path.join("a", "b", "c"))       # "a/b/c"
term.log(path.dirname("/a/b/c.txt"))     # "/a/b"
term.log(path.basename("/a/b/c.txt"))    # "c.txt"
term.log(path.ext("/a/b/c.txt"))         # ".txt"
term.log(path.stem("/a/b/c.txt"))        # "c"
term.log(path.resolve("a/../b"))         # "b"
```

Locating a file relative to the current module (a common pattern used by other standard
library modules to find their own native code):

```
import "path" as path

let __rand__ = load_native_code(
    path.resolve(program.file.directory, "native", "bin", "random" + platform.lib_ext)
)
```

(from [`stdlib/random/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/random/init.ar) — see
[`program`](../../language/modules/README.md#the-program-global) and
[`platform`](../../language/modules/README.md#the-platform-global) for the globals used
here)

## `path.expand_user`

```
let home = do
    if (__is_windows__) return __normalise__(env["USERPROFILE"])
    return env["HOME"]
```

(from [`stdlib/path/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/path/init.ar)) — this shows the
[`env`](../../language/modules/README.md#the-env-global) global being used to find the
user's home directory in a cross-platform way.

## Next

- [`json`](../json/README.md)