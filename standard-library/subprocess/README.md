# `subprocess`

```argon
import "subprocess" as subprocess
```

Runs other programs.

## `subprocess.run(args, stdin=file.stdin, stdout=file.stdout, stderr=file.stderr, cwd=null)`

Runs a program, waits for it to finish and returns its exit code. `args` is an array: the
program followed by its arguments. The program is found using `PATH`, and no shell is
involved, so arguments don't need quoting:

```argon
import "subprocess" as subprocess

let code = subprocess.run(["git", "--version"])
term.log("exit code:", code)
```

By default the child process shares the current program's standard input, output and
error. Pass other [`File`](../file/README.md) objects to redirect them, or `file.stdnull` to
connect a stream to the null device (discarding output, or giving empty input). `cwd` sets
the working directory:

```argon
import "subprocess" as subprocess
import "file" as file

let out = file.open("listing.txt", "w")
let code = subprocess.run(["ls", "-l"], stdout = out, cwd = "/tmp")
out.close()

let f = file.open("listing.txt")
term.log(code, f.read().length > 0)   # 0 true
f.close()
```

Discarding a command's output:

```argon
import "subprocess" as subprocess
import "file" as file

let code = subprocess.run(["ls", "-l"], stdout = file.stdnull, stderr = file.stdnull)
term.log(code)   # 0
```

To run a shell command, run the shell explicitly:

```argon
import "subprocess" as subprocess
term.log(subprocess.run(["sh", "-c", "echo hello; exit 3"]))   # prints hello, then 3
```

## `subprocess.start(args, ...)`

Starts a program **without** waiting for it, taking the same arguments as `run`. It returns
a `Process`:

| Member | Description |
|---|---|
| `wait()` | wait for the process to exit and return its exit code |
| `exit_code` | `null` until `wait()` returns, then the exit code |

```argon
import "subprocess" as subprocess

let process = subprocess.start(["sleep", "1"])
term.log("started")
term.log("exited with", process.wait())
```

## Next

- [`archive`](../archive/README.md)
