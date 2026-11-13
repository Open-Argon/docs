# `path`

```argon
import "path" as path
```

Path manipulation, written in Argon. These functions only work on strings and never touch
the filesystem. Use the [`file`](../file/README.md) module to check whether paths exist.

Paths always use `/` as the separator. On Windows, `\` is converted to `/`.

## Reference

| Function | Description |
|---|---|
| `path.sep` | the path separator (`"/"`) |
| `path.os` | the current `platform.os` |
| `path.join(*parts)` | join components with `/` |
| `path.resolve(*parts)` | join components and resolve `.` and `..` |
| `path.normalise(p)` | normalise separators and remove trailing slashes |
| `path.split(p)` | `tuple(head, tail)` |
| `path.dirname(p)` | everything before the last component |
| `path.basename(p)` | the last component |
| `path.splitext(p)` | `tuple(root, ext)` |
| `path.ext(p)` | the extension, including the dot, e.g. `".txt"` |
| `path.stem(p)` | the last component without its extension |
| `path.parts(p)` | an array of components (an absolute path starts with `"/"`) |
| `path.is_absolute(p)` / `path.is_relative(p)` | whether the path is absolute |
| `path.relative_to(p, base)` | `p` expressed relative to `base` (throws if it isn't inside `base`) |
| `path.is_relative_to(p, base)` | whether `p` is inside `base` |
| `path.with_name(p, name)` | replace the last component |
| `path.with_stem(p, stem)` | replace the stem, keeping the extension |
| `path.with_ext(p, ext)` | replace the extension |
| `path.common_prefix(paths)` | the longest common leading path of an array of paths |
| `path.expand_user(p)` | expand a leading `~` to the user's home directory |
| `path.user_directory()` | the user's home directory |
| `path.config_directory()` | the user's configuration directory (see below) |
| `path.is_hidden(p)` | whether the last component starts with `.` |

## Examples

```argon
import "path" as path

term.log(path.join("a", "b", "c"))              # a/b/c
term.log(path.resolve("/x", "y", "../z"))       # /x/z
term.log(path.dirname("/a/b/c.txt"))            # /a/b
term.log(path.basename("/a/b/c.txt"))           # c.txt
term.log(path.ext("/a/b/c.txt"))                # .txt
term.log(path.stem("/a/b/c.txt"))               # c
term.log(path.split("/a/b/c"))                  # tuple("/a/b", "c")
term.log(path.splitext("archive.tar.gz"))       # tuple("archive.tar", ".gz")
term.log(path.parts("/a/b/c"))                  # ["/", "a", "b", "c"]
term.log(path.relative_to("/a/b/c", "/a"))      # b/c
term.log(path.with_ext("/a/b.txt", ".md"))      # /a/b.md
term.log(path.common_prefix(["/a/b/c", "/a/b/d"]))   # /a/b
term.log(path.is_hidden(".git"))                # true
```

Finding a file next to the current module:

```argon
import "path" as path
let config = path.join(program.file.directory, "config.json")
```

## Configuration directory

`path.config_directory()` returns the conventional place for per-user configuration:

| OS | Directory |
|---|---|
| Linux / FreeBSD | `$XDG_CONFIG_HOME`, or `~/.config` |
| macOS | `~/Library/Application Support` |
| Windows | `%APPDATA%` |

## Next

- [`file`](../file/README.md)
