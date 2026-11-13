# Standard library

The standard library ships with every Chloride installation (in the `stdlib/` directory
next to the interpreter). Modules are imported by name:

```argon
import "json" as json
import "file" expose open, is_file
```

Most modules are written in Argon, with native C code for anything that needs the operating
system. See [Modules and `import`](../language/modules/README.md) for how imports work, and
note that a local file with the same name as a module (e.g. `json.ar`) takes priority over
the standard library.

| Module | Description |
|---|---|
| [`term`](term/README.md) | printing and reading from the terminal (global, no import needed) |
| [`maths`](maths/README.md) | rounding, absolute value, min and max |
| [`random`](random/README.md) | random numbers |
| [`path`](path/README.md) | path manipulation |
| [`file`](file/README.md) | files, directories and the standard streams |
| [`json`](json/README.md) | JSON encoding and decoding |
| [`date`](date/README.md) | dates, times and formatting |
| [`time`](time/README.md) | sleeping |
| [`regex`](regex/README.md) | regular expressions (PCRE2) |
| [`network`](network/README.md) | TCP and TLS sockets, and an HTTP/HTTPS client |
| [`url`](url/README.md) | URL encoding and query strings |
| [`hex`](hex/README.md) | hexadecimal formatting |
| [`threading`](threading/README.md) | threads and thread-local storage |
| [`subprocess`](subprocess/README.md) | running other programs |
| [`archive`](archive/README.md) | reading and writing tar, zip and other archives |
| [`build`](build/README.md) | compiling native extensions |
