# `file`

```argon
import "file" as file
```

Files, directories and the standard streams.

## Opening files: `open(path, mode="r")`

```argon
import "file" expose open

let f = open("notes.txt", "w")
f.write("first line\n")
f.close()

f = open("notes.txt")
term.log(f.read())
f.close()
```

The mode is a base mode, optionally followed by modifiers:

| Base | Meaning |
|---|---|
| `r` | read (the file must exist) |
| `w` | write, creating or truncating the file |
| `a` | append, creating the file if needed |
| `x` | create a new file, failing if it already exists |

| Modifier | Meaning |
|---|---|
| `b` | binary: read and write [buffers](../../language/buffers/README.md) instead of strings |
| `+` | open for both reading and writing |

For example: `"rb"`, `"w+"`, `"ab"`. An invalid mode throws `file.FileError`.

## `File` methods

| Method | Description |
|---|---|
| `.read(size?)` | read up to `size` bytes, or the whole file if `size` is omitted |
| `.write(data)` | write a string (text mode) or buffer (binary mode); returns the number of bytes written |
| `.seek(offset, whence=0)` | move the position: `whence` is `0` (from the start), `1` (from the current position) or `2` (from the end) |
| `.tell()` | the current position |
| `.flush()` | flush buffered writes |
| `.size()` | the file's size in bytes |
| `.close()` | close the file (closing twice is harmless) |

Files also have `.mode` and `.closed` attributes.

In text mode, `read` returns a string, and reading at the end of the file returns `""`. In
binary mode, it returns a buffer.

Reads start at the current position, so `.read()` after `.read(5)` returns the rest of the
file:

```argon
import "file" as file

let p = file.temp_file("demo-*.txt")
let f = file.open(p, "w")
f.write("hello world")
f.close()

f = file.open(p)
term.log(f.read(6))   # hello
term.log(f.read())    # world
f.close()
file.delete_file(p)
```

## Standard streams

`file.stdin`, `file.stdout`, `file.stderr` and `file.stdnull` are already-open `File`
objects in **binary** mode, so they read and write buffers:

```argon
import "file" as file

file.stdout.write(buffer.from_string("to stdout\n"))
file.stderr.write(buffer.from_string("to stderr\n"))
```

`file.stdin.read()` reads all of standard input as a buffer, which is useful when a
program is used in a pipeline:

```
let input = file.stdin.read().to_string()
```

## Files and directories

| Function | Description |
|---|---|
| `file.is_file(path)` | whether `path` is an existing file |
| `file.is_directory(path)` | whether `path` is an existing directory |
| `file.get_metadata(path)` | `{size, mtime, mtime_ns, device, inode}` |
| `file.mkdir(path)` | create a directory |
| `file.makedirs(path)` | create a directory and any missing parents; no error if it already exists |
| `file.tree_dir(path)` | array of every file and directory under `path` (recursive, absolute paths) |
| `file.copy_file(source, destination)` | copy a file |
| `file.copy_dir(source, destination)` | copy a directory recursively |
| `file.move(source, destination)` | move or rename a file or directory |
| `file.move_contents(source, destination)` | move everything inside `source` into `destination` |
| `file.delete_file(path)` | delete a file |
| `file.delete_dir(path)` | delete a directory and everything in it |
| `file.temp_file(pattern)` | create an empty temporary file and return its path |
| `file.temp_dir(pattern)` | create a temporary directory and return its path |

The temporary file functions replace the `*` in `pattern` with random characters:

```argon
import "file" as file
import "path" as path

let dir = file.temp_dir("demo-*")
let target = path.join(dir, "a", "b")
file.makedirs(target)

let f = file.open(path.join(target, "hello.txt"), "w")
f.write("hi")
f.close()

for (entry in file.tree_dir(dir)) term.log(path.relative_to(entry, dir))
file.delete_dir(dir)
```

## Exceptions

The `file` module's exceptions are available as `file.<name>`:

- `FileError`: the base class for file errors (the same class as the global `FileError`)
  - `FileNotFoundError`
  - `FilePermissionError`
  - `FileExistsError`
  - `FileReadError`: e.g. reading from a file that wasn't opened for reading
  - `PathError`: the same class as the global `PathError`
  - `TempDirectoryError`

Catch a specific error, or `FileError` for any of them:

```argon
import "file" as file

try do
    file.open("/does/not/exist")
catch (file.FileNotFoundError as e) do
    term.log("not found:", e.message)

try file.open("/does/not/exist")
catch (FileError as e) term.log(type(e).__name__)   # FileNotFoundError
```

## Next

- [`json`](../json/README.md)
