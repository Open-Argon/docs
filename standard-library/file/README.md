# `file`

```
import "file" expose open
```

Filesystem I/O (native code backed), see
[`stdlib/file/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/file/init.ar).

## `open(path, mode="r")`

Opens a file and returns a `File` object. `mode` follows familiar conventions:

- Base mode: `"r"` (read), `"w"` (write, truncating), `"a"` (append), `"x"` (exclusive
  create).
- Modifiers: `"b"` (binary) and `"+"` (read/write), which can be combined with the base
  mode, e.g. `"rb"`, `"w+"`.

```
import "file" expose open

let f = open("/dev/stdout", "w")
f.write("hello world\n")
f.close()
```

(from [`file_io.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/tests/file_io.ar))

## `File` methods

| Method | Description |
|---|---|
| `.read(size?)` | read up to `size` bytes/characters, or the whole file if omitted |
| `.write(data)` | write `data` to the file |
| `.close()` | close the file handle |
| `.seek(offset, whence=0)` | move the file position |
| `.tell()` | get the current file position |
| `.flush()` | flush any buffered writes |
| `.size()` | the file's size in bytes |

Note `.read(size?)` uses a **`?` suffix on the parameter name** to mark it optional (distinct
from giving it a default value) — calling `.read()` with no arguments reads the entire
file.

## Exceptions

`file` defines its own exception hierarchy (all subclasses of the base `FileError`), from
[`stdlib/file/exceptions.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/file/exceptions.ar):

- `FileError` — general file error base class.
  - `FileReadError` — raised when reading fails (e.g. reading a file not opened in read
    mode).
  - `FileNotFoundError`
  - `FilePermissionError`
  - `FileExistsError`

```
import "file" expose open, FileNotFoundError

try do
    let f = open("/does/not/exist", "r")
catch (FileNotFoundError as e) do
    term.log("file not found:", e.message)
```

## Modes are validated

Attempting to open a file with an invalid mode string raises a `FileError` before any
filesystem access happens — see `_validate_mode` in
[`stdlib/file/init.ar`](https://git.wbell.dev/Open-Argon/Chloride/src/commit/8e1ee06b88200ab112cf34e49efe969030ea435c/stdlib/file/init.ar), which also demonstrates
Argon's `not in` operator:

```
if (mode[0] not in valid_base) do
    throw FileError("invalid mode: '" + mode + "'")
```

## Next

- [`date`](../date/README.md)