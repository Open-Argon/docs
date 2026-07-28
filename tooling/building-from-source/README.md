# Build from source

If the pre-built binaries do not meet your requirements, Chloride can be built from source for your platform.

## Getting the source

Clone the Chloride repository:

```
git clone https://git.wbell.dev/Open-Argon/Chloride
cd Chloride
```

Chloride uses git submodules, which must be downloaded recursively:

```
git submodule update --init --recursive
```

Before building Chloride, compile the standard libraries:

```
./build-stdlib.sh
```

## Build systems

There are two supported ways to build Chloride:

- **Conan** — recommended for users who are not developing Chloride.
- **Make** — recommended for Chloride development, as it supports dynamic linking and provides additional debugging tools.

## Conan

Conan is a cross-platform package manager and build tool.

The required dependencies are:

- `conan`
- `flex`
- `cmake`
- `gcc`

Install the dependencies using Conan:

```
conan install . --build=missing
```

Then build Chloride:

```
conan build .
```

The final build can be found in:

```
build/dist/bin
```

## Make

Make is intended primarily for Chloride development.

Unlike Conan, dependencies are not automatically managed. The exact dependencies depend on your platform and may change over time. If a build fails due to a missing dependency, check the `Makefile` or install the missing package manually.

Development is currently only configured for POSIX systems. If you are using Windows, it is recommended to build using **WSL**.

### Normal build

To build Chloride normally:

```
make -j$(nproc)
```

### Cross-compiling for Windows

To build a Windows version from a POSIX system:

```
make -j$(nproc) TARGET_OS=windows
```

### Debug builds

For a debug build:

```
make -j$(nproc) full-debug
```

For a Windows debug build:

```
make -j$(nproc) full-debug TARGET_OS=windows
```