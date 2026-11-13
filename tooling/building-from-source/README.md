# Building Chloride from source

If the pre-built packages don't meet your needs, Chloride can be built from source.

## Getting the source

Clone the Chloride repository:

```bash
git clone https://git.wbell.dev/Open-Argon/Chloride
cd Chloride
```

Chloride uses git submodules, which must be downloaded recursively:

```bash
git submodule update --init --recursive
```

Before building Chloride, compile the standard library's native code:

```bash
./build-stdlib.sh
```

## Build systems

There are two supported ways to build Chloride:

- **Conan**: recommended if you just want to use Chloride. Dependencies are managed for
  you.
- **Make**: recommended for Chloride development, as it supports dynamic linking and has
  debugging builds.

## Conan

[Conan](https://conan.io/) is a cross-platform package manager and build tool.

The required tools are:

- `conan`
- `flex`
- `cmake`
- a C compiler, e.g. `gcc`

Install the dependencies using Conan:

```bash
conan install . --build=missing
```

Then build Chloride:

```bash
conan build .
```

The build can be found in:

```
build/dist/bin
```

## Make

Make is intended primarily for Chloride development.

Unlike Conan, dependencies are not automatically managed. The exact dependencies depend on
your platform and may change over time. If a build fails because of a missing dependency,
check the `Makefile` or install the missing package manually.

Development is currently only configured for POSIX systems. If you are using Windows, build
using **WSL**.

| Command | Builds |
|---|---|
| `make -j$(nproc)` | a normal build |
| `make -j$(nproc) native` | an optimised build for this machine's CPU (`-march=native -O3`) |
| `make -j$(nproc) debug` | a debug build with symbols and interpreter debug output |
| `make -j$(nproc) full-debug` | a debug build with AddressSanitizer |

Add `TARGET_OS=windows` to cross-compile a Windows build from a POSIX system:

```bash
make -j$(nproc) TARGET_OS=windows
make -j$(nproc) full-debug TARGET_OS=windows
```

## Installation layout

When running a build, keep this layout, because the interpreter finds the standard library
and global packages relative to its own executable:

```
<prefix>/
├── bin/
│   ├── argon
│   └── isotope          (launcher script that runs `argon isotope`)
├── stdlib/              (the standard library)
├── argon_modules/       (globally installed packages, including isotope)
└── include/             (Argon.h and friends, for native extensions)
```
