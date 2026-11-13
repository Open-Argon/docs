# `argon-package.json` and `iso-lock.json`

Both files live in the project's root directory and are created by `isotope init` (or the
first `isotope install`). Isotope rewrites them whenever you install, update or remove a
package. Commit both to version control.

## `argon-package.json`

Describes the project or package:

```json
{
    "name": "my-app",
    "version": "1.0.0",
    "dependencies": {
        "markdown": "0.0.1",
        "sqlite": "1.1.0"
    }
}
```

| Field | Description |
|---|---|
| `name` | the package name; `isotope init` uses the directory name |
| `version` | the package version; `isotope init` uses `"1.0.0"` |
| `dependencies` | packages this project needs, as `"name": "version"` |
| `build` | *(optional)* path to an Argon script to run at install time (see [Publishing](../publishing/README.md#packages-with-native-code)) |

Isotope keeps the dependency versions up to date as you install and update packages. You
can also edit them by hand and then run `isotope install`.

## `iso-lock.json`

Records exactly where every installed package, including indirect dependencies, was
downloaded from:

```json
{
    "packages": {
        "markdown@0.0.1": {
            "remote": "isotope.wbell.dev",
            "url": "https://isotope.wbell.dev/isotope-download?name=markdown&version=0.0.1",
            "dependencies": {}
        }
    }
}
```

| Field | Description |
|---|---|
| `packages` | one entry per installed package, keyed by `name@version` |
| `remote` | the registry the package came from |
| `url` | the archive download URL |
| `dependencies` | that package's own dependencies, as `"name": "version"` |

When a package is listed in the lock file, `isotope install` downloads it from the recorded
`url` instead of asking the registry again. You shouldn't normally need to edit this file.

## `argon_modules/`

Installed packages are unpacked into `argon_modules/<name>/`. Each package's own
dependencies are installed into its own nested `argon_modules/` directory. Don't commit
`argon_modules/`; it can always be recreated with `isotope install`.
