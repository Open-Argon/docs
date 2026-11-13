# Argon standard library quick reference

Import with `import "<module>" as <name>` or `import "<module>" expose a, b`. `term` is
global. Describes Chloride v4.1.0 (see "If you're on v4.0.3 or older" in SKILL.md).

## maths

`abs(n)`, `int(n)` (toward zero), `floor(n)`, `ceil(n)`, `round(n, precision=1)`,
`max(*nums)`, `min(*nums)` (separate arguments: `maths.max(*arr)`).

## random

```argon
import "random" expose random, Random
term.log(random.random())               # [0, 1)
term.log(random.int(1, 7))              # [1, 7): end is excluded, so 1 to 6
term.log(random.choice(["a", "b"]))
let seeded = Random(42)                  # reproducible
term.log(seeded.random() == Random(42).random())   # true
```

## path (pure string operations, `/` separators)

`join(*p)`, `resolve(*p)` (handles `..`), `normalise(p)`, `split(p)` gives
`tuple(head, tail)`, `dirname`, `basename`, `splitext(p)` gives `tuple(root, ext)`, `ext`,
`stem`, `parts`, `is_absolute`, `is_relative`, `relative_to(p, base)`,
`is_relative_to(p, base)`, `with_name`, `with_stem`, `with_ext`, `common_prefix(paths)`,
`expand_user(p)`, `user_directory()`, `config_directory()`, `is_hidden(p)`, `sep`, `os`.

## file

```argon
import "file" as file
import "path" as path

let dir = file.temp_dir("demo-*")
let p = path.join(dir, "notes.txt")
let f = file.open(p, "w")                 # modes: r w a x, plus b (binary/buffers) and +
f.write("hello\n")
f.close()
f = file.open(p)
term.log(f.read())                        # rest of the file; f.read(n) reads n bytes
f.close()
term.log(file.is_file(p), file.is_directory(dir), file.get_metadata(p).size)
file.delete_dir(dir)
```

- Files: `.read(size?)`, `.write(data)` (returns bytes written), `.seek(offset, whence=0)`,
  `.tell()`, `.flush()`, `.size()`, `.close()`, `.mode`, `.closed`.
- Functions: `is_file`, `is_directory`, `get_metadata` (gives
  `{size, mtime, mtime_ns, device, inode}`), `mkdir`, `makedirs`, `tree_dir` (recursive
  absolute paths), `copy_file`, `copy_dir`, `move`, `move_contents`, `delete_file`,
  `delete_dir`, `temp_file(pattern)`, `temp_dir(pattern)` (`*` is replaced randomly).
- Streams `file.stdin`, `file.stdout`, `file.stderr` and `file.stdnull` are **binary**:
  `file.stderr.write(buffer.from_string("msg\n"))`; `file.stdin.read().to_string()`.
- Exceptions: `file.FileError` (the same class as the global `FileError`) >
  `FileNotFoundError`, `FilePermissionError`, `FileExistsError`, `FileReadError`, `PathError`,
  `TempDirectoryError`. `catch (FileError as e)` catches all of them.

## json

```argon
import "json" as json
let text = json.stringify({a: [1, 2], b: null})              # {"a":[1,2],"b":null}
term.log(json.stringify({a: 1}, pretty = true, indent = "  "))
let data = json.parse(text)
term.log(data.a[1])                                          # 2
```

Tuples are written as arrays. Objects with a `__json__(self, pretty, parents)` method are
serialised by it, and other objects throw `TypeError`. Parse errors throw `ValueError`.

## date

```argon
import "date" as date
let D = date.Date
let d = D(year = 2026, month = 7, day = 28, hour = 15)    # fields are UTC; utc = false for local
term.log(d, d.to_string("%d/%m/%Y"), d.http_date())      # prints as ISO: 2026-07-28T15:00:00.000Z
term.log(d.year, d.month, d.day, d.millisecond, d.day_of_week, d.weekday_name, d.month_name)
let parsed = D.from_string("28/07/2026 09:30", "%d/%m/%Y %H:%M")   # UTC; utc = false for local; ValueError if no match
let iso = D.from_iso_string("2026-07-28T15:30:00.250+01:00")      # Z / offsets respected
let gap = D(year = 2026, month = 12, day = 25) - d                 # Date - Date gives a Duration
term.log(gap, gap.total_days, d + date.Duration(days = 1, hours = 2), d < iso)
term.log(D(year = 2026, month = 1, day = 31).add_months(1))        # clamps: 2026-02-28
term.log(d.replace(day = 1), d.start_of_day(), d.local().hour, d.utc_offset())
```

- Constructors: `Date(timestamp_ms=)`, `Date(timestamp_s=)`, `Date(date=other)`, calendar
  fields (`year`, `month`, `day`, `hour`, `minute`, `second`, `millisecond`, `utc=true`),
  `Date.now()`, `Date.today(utc=true)`, `from_string(text, fmt, utc=true)` and
  `from_iso_string(text, utc=true)`.
- Fields: `year`, `month`, `day`, `hour`, `minute`, `second`, `millisecond`, `day_of_week`
  (0 is Sunday), `day_of_year`, `weekday_name`, `month_name`, `is_leap_year`,
  `days_in_month`. Assigning a field mutates in place; `replace(...)` returns a copy.
- Arithmetic: `add(weeks, days, hours, minutes, seconds, milliseconds)`, `add_months(n)`,
  `add_years(n)`, `start_of_day`, `end_of_day`, `start_of_month`, `start_of_year` (each
  takes `utc=true`), `date ± Duration`, `date - date`, comparisons, and use as dictionary
  keys.
- `Duration(weeks, days, hours, minutes, seconds, milliseconds)` supports `+ - * /`,
  negation, comparisons and `total_milliseconds/seconds/minutes/hours/days`, and prints as
  `N days, HH:MM:SS.mmm`.
- Local time: `local()` (a dictionary of local fields plus `utc_offset`), `utc_offset()`,
  `to_local_string(fmt)`, `to_local_iso_string()`.
- Output: `to_string(fmt)` (UTC), `to_iso_string()`, `http_date()`, `unix()`, `unix_ms()`.
  `Date.monotonic()` is in nanoseconds.
- Module functions: `date.is_leap_year(y)`, `date.days_in_month(y, m)`, `date.WEEKDAYS`,
  `date.MONTHS`.

## time

`time.snooze(seconds)` sleeps (fractions allowed).

## regex (PCRE2; escape backslashes: `"\\d+"`)

```argon
import "regex" as regex
let re = regex.Regex("([a-z]+)([0-9]+)")
term.log(re.match("x abc1"))          # true (searches anywhere; anchor with ^ $)
term.log(re.find("abc1 de2"))         # ["abc1", "abc", "1"], or [] if there's no match
term.log(re.find_all("abc1 de2"))     # [["abc1", "abc", "1"], ["de2", "de", "2"]]
term.log(re.group("abc1", 2))         # 1
term.log(re.replace_all("abc1 de2", "N"), regex.Regex(",\\s*").split("a, b,c"))
let cached = regex.Regex.intern("^[a-z]+$")   # shared/cached, can't be closed
```

Also `.replace(s, r)` (first match) and `.close()`. Invalid patterns throw `regex.RegexError`.

## network

HTTP(S) client:

```
import "network" expose http, multipart_form
let resp = http.get("https://example.com/", headers = {"Accept": "text/html"})
resp.status; resp.ok(); resp.headers; resp.text(); resp.json(); resp.bytes()
for (chunk in resp) ...          # stream body as buffers (body readable once)
http.post(url, body = "...", headers = {...})   # also put, del, request(method, url, headers, body)
# kwargs: verify_peer=true, ca_path=null, timeout_ms=30000, auto_close=true
let form = multipart_form()
form.field("name", "Ada")
form.file("upload", "a.txt", file.open("a.txt", "rb"))
http.post(url, body = form, headers = {"Content-Type": form.content_type()})
```

Non-2xx responses don't throw. Errors throw `network.HTTPError`, `network.SocketError` or
`network.TLSError`. `resp.status_text` is the reason phrase (`"Not Found"`).

TCP: `network.tcp(port)` (server; `.accept()` gives a connection) and
`network.tcp_client(host, port)`. Connections support `send(buffer)`, `send_string(s)`,
`recv(buffer)` (returns a count), `recv_string(size)`, `peek`,
`poll(read, write, timeout_ms)` (bitmask `NET_POLL_READ=1`, `NET_POLL_WRITE=2`; `0` means
timeout), `set_nonblocking`, `set_opt(network.NET_OPT_NODELAY, true)` and `close()`. TLS:
`network.tls_client(host, port, verify_peer=true, ca_path=null)`.

## url / hex

`url.encode(v)`, `url.decode(s)`, `url.queryStr({k: "v", n: 2})`,
`url.decodeURLQuery("a=1&b=2")`. `hex(255)` gives `"FF"` and `hex(-255)` gives `"-FF"`
(`import "hex" expose hex`);
parse hex with `number("0x" + s)`.

## threading

```argon
import "threading" as threading
let t = threading.Thread(() = 6 * 7).start()   # start() returns the thread
term.log(t.join())                              # 42; exceptions in the thread rethrow here
let local = threading.Local()                   # per-thread attributes/items
local.value = 1
```

Pass data to threads by closing over a variable declared inside the loop body
(`let n = i`). Also `t.detach()` and `threading.get_id()`.

## subprocess

```argon
import "subprocess" as subprocess
let code = subprocess.run(["sh", "-c", "exit 3"])       # waits; returns the exit code
term.log(code)
let p = subprocess.start(["true"])                      # doesn't wait
term.log(p.wait(), p.exit_code)
```

kwargs: `stdin`, `stdout`, `stderr` (File objects; redirect stdout to a file opened with
`"w"`), `cwd`. No shell is used unless you run one.

## archive (libarchive)

```
import "archive" as archive
archive.extract("pkg.tar.gz", "out_dir")
archive.create("out.tar.gz", [{archive_path: "a.txt", src_path: "/abs/a.txt"}], "tar", "gzip")
let w = archive.ArchiveWriter("x.zip", "zip", "none")   # formats: tar zip cpio 7zip iso9660; filters: none gzip bzip2 xz zstd
w.add_file(name, src); w.add_bytes(name, data); w.add_dir(name); w.close()
let r = archive.ArchiveReader(path_or_buffer)
let e = r.next()      # ArchiveEntry {path, size, is_dir, mtime, mode} or null
r.read(); r.read_chunk(n); r.extract_to(dest); r.extract_all(dir); r.close()
```

## build (compile native extensions; never name your script build.ar)

```
import "build" as build
build.Compiler("c").shared()
    .source("src/native/main.c")
    .library_if({"linux": ["pthread", "dl"], "darwin": ["pthread"], "windows": []})
    .output("src/native/bin", "mylib")      # gives src/native/bin/<os>/<arch>/libmylib.so
    .build()                                # paths are relative to the working directory
import "build/lib_path" expose lib_path     # no compiler detection
let native = load_native_code(lib_path(program.file.directory + "native/bin", "mylib"))
```

Other methods: `include`, `define`, `library`, `library_path`, `link`, `flag` (each with an
`_if` variant), `target(os)`, `arch(a)`, `prefer(name)`, `output_exact(path)`,
`build_for_targets([[os, arch], ...])`.

## Native extension (C)

```c
#include <Argon.h>
#include <ArgonFunction.h>

ARGON_FUNCTION(add, {
  if (api->fix_to_arg_size(2, argc, err)) return api->ARGON_NULL;
  int64_t a = api->argon_to_i64(argv[0], err);
  if (api->is_error(err)) return api->ARGON_NULL;
  int64_t b = api->argon_to_i64(argv[1], err);
  if (api->is_error(err)) return api->ARGON_NULL;
  return api->i64_to_argon(a + b);
})

INIT_ARGON_MODULE(
  REGISTER_ARGON_FUNCTION(add)
)
```

Function parameters are `argc, argv, kwargs, err, state, api`. Throw with
`return api->throw_argon_error(err, api->RuntimeError, "fmt %d", x);`. Converters:
`argon_to_i64/double/rational/string/array`, `argon_buffer_to_buffer`, and
`i64/double/rational/string_to_argon`. `struct string` is `{data, length}` and not
null-terminated. `load_native_code(path)` returns a dictionary of the registered names.
`native_code_example/` in the Chloride repo uses an outdated signature, so don't copy it.
