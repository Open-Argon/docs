# `archive`

```argon
import "archive" as archive
```

Reads and writes archive files (tar, zip, 7z and more, via libarchive). Isotope uses this
module for package tarballs.

## Quick functions

| Function | Description |
|---|---|
| `archive.extract(source, dest_dir)` | extract every file in an archive into `dest_dir` |
| `archive.create(dest_path, entries, format, filter)` | create an archive from files on disk |

`entries` is an array of `{archive_path, src_path}` dictionaries:

```
import "archive" as archive

archive.create("backup.tar.gz", [
    {archive_path: "notes.txt", src_path: "/home/me/notes.txt"},
], "tar", "gzip")

archive.extract("backup.tar.gz", "restored")
```

## `ArchiveWriter(dest, format, filter)`

Writes a new archive at `dest`.

- `format`: `"tar"`, `"zip"`, `"cpio"`, `"7zip"` or `"iso9660"`
- `filter` (compression): `"none"`, `"gzip"`, `"bzip2"`, `"xz"` or `"zstd"`. Formats with
  built-in compression, such as zip, ignore it.

| Method | Description |
|---|---|
| `add_file(archive_path, src_path)` | add a file from disk, stored as `archive_path` |
| `add_bytes(archive_path, data)` | add a file with the given contents (a string or buffer) |
| `add_dir(archive_path)` | add an empty directory |
| `close()` | finish writing the archive. **Always call this.** |

## `ArchiveReader(source)`

Opens an archive for reading. `source` is a file path, or a buffer containing archive data
(e.g. a downloaded file). The format and compression are detected automatically.

| Method | Description |
|---|---|
| `next()` | move to the next entry and return it as an `ArchiveEntry`, or `null` at the end |
| `read()` | read the current entry's contents |
| `read_chunk(max_len)` | read up to `max_len` bytes of the current entry, or `null` when done |
| `extract_to(dest_path)` | write the current entry to `dest_path` |
| `extract_all(dest_dir)` | extract all remaining entries into `dest_dir` |
| `close()` | close the archive |

Each `ArchiveEntry` has `path`, `size`, `is_dir`, `mtime` and `mode` attributes.

```argon
import "archive" as archive
import "file" as file

let dir = file.temp_dir("archive-demo-*")
let tarball = dir + "/demo.tar.gz"

let writer = archive.ArchiveWriter(tarball, "tar", "gzip")
writer.add_bytes("hello.txt", "hello from an archive")
writer.add_dir("empty")
writer.close()

let reader = archive.ArchiveReader(tarball)
let entry = reader.next()
while (entry != null) do
    if (entry.is_dir) term.log("dir: ", entry.path)
    else term.log("file:", entry.path, entry.size, reader.read())
    entry = reader.next()
reader.close()

file.delete_dir(dir)
```

Errors throw `archive.ArchiveError`.

## Next

- [`build`](../build/README.md)
