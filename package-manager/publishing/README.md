# Publishing packages

## 1. Set up an account and token

1. Create an account at [isotope.wbell.dev/register](https://isotope.wbell.dev/register)
   (or log in).
2. Create a repository for your package on the [create](https://isotope.wbell.dev/create)
   page. The package name must match the name in your `argon-package.json`.
3. Get an access token from your [account page](https://isotope.wbell.dev/account) and save
   it:

   ```bash
   isotope auth add isotope.wbell.dev <token>
   ```

## 2. Create the package

```bash
mkdir my-package && cd my-package
isotope init
```

Edit `argon-package.json` to set the name and version:

```json
{
    "name": "my-package",
    "version": "1.0.0",
    "dependencies": {}
}
```

- **Name:** 3 to 32 characters: lowercase letters, digits, `-` and `.`. It must start
  and end with a letter or digit.
- **Version:** up to 16 characters, e.g. `1.0.0`.

Put your code in a `src/` directory, with `src/init.ar` as the entry point. When someone
installs the package, the **contents** of `src/` become the package directory, so
`import "my-package"` loads `src/init.ar`:

```
my-package/
├── argon-package.json
├── iso-lock.json
├── LICENSE
└── src/
    ├── init.ar
    └── helpers.ar
```

Inside the package, import your own files with relative paths (`import "helpers"`).

Dependencies installed with `isotope install` while you develop are recorded in
`argon-package.json`, and will be installed automatically for anyone who installs your
package.

## 3. Build

From the directory containing `argon-package.json`:

```bash
isotope package build
```

This creates `__isotope__/<name>@<version>.tar.gz` containing:

- `argon-package.json` and `iso-lock.json`
- a licence file, if there is one named `LICENSE`, `LICENCE`, `LICENSE.txt` or `LICENCE.txt`
- the build script, if `argon-package.json` names one (see below)
- everything in `src/`, except `__arcache__` directories

Add `__isotope__/` to your `.gitignore`.

## 4. Publish

```bash
isotope package push            # publish as a pre-release
isotope package push --latest   # publish as the latest stable release
```

`isotope install my-package` without a version installs the latest stable release.

> **Note (Isotope 1.0.0):** `push` accepts `--remote <host>` but currently always publishes
> to the default remote. To publish elsewhere, change `default_remote` in Isotope's
> [`config.json`](../README.md#configuration).

To publish a new version, change `version` in `argon-package.json`, then build and push
again.

## Packages with native code

A package can compile [native extensions](../../tooling/native-extensions/README.md) on the
user's machine at install time. Set `build` in `argon-package.json` to the path of an Argon
script:

```json
{
    "name": "my-native-package",
    "version": "1.0.0",
    "build": "install.ar"
}
```

When the package is installed, Isotope runs the script with `argon`, **with the unpacked
package archive as the working directory** (so `src/` is still a subdirectory), and then
deletes it. A typical build script uses the [`build`](../../standard-library/build/README.md)
module, and skips compiling if a prebuilt binary for the platform is already included:

```
import "build" as build
import "file" expose is_file

let library = build.lib_path("src/native/bin", "mylib")

if (!is_file(library)) do
    build.Compiler("c")
        .shared()
        .source("src/native/main.c")
        .output("src/native/bin", "mylib")
        .build()
```

The package's `src/init.ar` then loads the library relative to itself:

```
import "build/lib_path" expose lib_path

let native = load_native_code(lib_path(program.file.directory + "native/bin", "mylib"))
```

Don't name the build script `build.ar`, because `import "build"` would import the script
itself.
