# Using packages

## Finding packages

Browse the packages on the default registry at
[isotope.wbell.dev/packages](https://isotope.wbell.dev/packages).

## Installing

From your project's root directory:

```bash
isotope install sqlite          # latest version
isotope install sqlite@1.1.0    # a specific version
isotope install sqlite haze     # several at once
```

Isotope:

1. creates `argon-package.json` and `iso-lock.json` if they don't exist yet (like
   `isotope init`),
2. downloads each package and its dependencies into `argon_modules/`,
3. runs the package's build script, if it has one (for example to compile native code),
4. records the package and version in `argon-package.json`, and the exact download location
   in `iso-lock.json`.

Then import the package by name from any file in the project:

```argon
import "sqlite" as sqlite
```

Imports search `argon_modules/` in the importing file's directory and then every parent
directory, so files in subdirectories of the project find the packages too. See
[how imports are resolved](../../language/modules/README.md#how-import-paths-are-resolved).

### Installing a project's dependencies

When you clone a project, run `isotope install` with no package names to install everything
listed in `argon-package.json`. Packages listed in `iso-lock.json` are downloaded from the
recorded location, so everyone gets the same versions.

Commit `argon-package.json` and `iso-lock.json` to version control, and add
`argon_modules/` to `.gitignore`.

## Updating

```bash
isotope update            # update every dependency to its latest version
isotope update sqlite     # update one package
```

`update` installs the latest version of each package and updates the version in
`argon-package.json`.

## Removing

```bash
isotope remove sqlite
```

This deletes `argon_modules/sqlite` and removes it from `argon-package.json` and
`iso-lock.json`.

## Global packages

Add `-g` (or `--global`) to `install`, `update` or `remove` to manage packages in the
installation's global `argon_modules` directory instead of the current project. Globally
installed packages can be imported by every Argon program on the machine, and a package
with an `init.ar` can be run directly with `argon <package-name>`. That's how
`argon isotope` works.

## Private registries

Some registries need an access token. Save one with:

```bash
isotope auth add isotope.wbell.dev <token>
```

On the default registry, tokens are created on your [account page](https://isotope.wbell.dev/account).
Isotope sends the token with every request to that host. Remove it with
`isotope auth remove <host>`.

## Troubleshooting

- **`InstallError: Package or version not found`**: check the package name and version on
  the registry.
- **`AuthError`**: the registry rejected your token, or needs one. See
  [Private registries](#private-registries).
- **`HTTPError: connection error while waiting for response headers`**: a network problem
  talking to the registry. Try again.
