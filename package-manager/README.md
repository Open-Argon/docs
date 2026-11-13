# Isotope

**Isotope** is Argon's package manager. It installs packages from a package registry
(by default [isotope.wbell.dev](https://isotope.wbell.dev/)) into an `argon_modules`
directory, where `import` finds them automatically.

Isotope is included with every Chloride installation. It's an Argon program itself:
`isotope <args>` is the same as running `argon isotope <args>`. It ships in the global
`argon_modules` directory of the installation.

## Quick start

```bash
mkdir my-app && cd my-app
isotope init               # create argon-package.json and iso-lock.json
isotope install markdown   # install a package and add it as a dependency
```

```argon
import "markdown" as markdown
```

Run `isotope install` with no package names in a project to install everything listed in
its `argon-package.json`, for example after cloning a repository.

## Commands

| Command | Description |
|---|---|
| `isotope init` | create `argon-package.json` and `iso-lock.json` in the current directory |
| `isotope install [package...] [--remote host] [-g]` | install packages, or all dependencies if none are named |
| `isotope remove <package...> [-g]` | remove packages |
| `isotope update [package...] [-g]` | update packages (or all dependencies) to their latest version |
| `isotope package build` | build a package archive from the current project |
| `isotope package push [--latest]` | publish the built archive to the registry |
| `isotope auth add <host> <token>` | save an access token for a registry |
| `isotope auth remove <host>` | delete a saved token |
| `isotope version` | print Isotope's version |
| `isotope help` | show the command list |

Packages are named `name` or `name@version`. Without a version, the latest version is
installed.

The `-g` / `--global` flag installs into (or removes from) the installation's global
`argon_modules` directory, so every program on the machine can import the package. This
usually needs administrator rights.

> **Known issue (Isotope 1.0.0):** `remove` and `update` also treat `-g` as a package name,
> so `isotope remove -g <package>` and `isotope update -g` fail with "package -g is not a
> dependency". `install -g` works.

`--remote host` installs from a different registry. Installing a package records which
registry it came from in `iso-lock.json`, so later installs and updates use the same one.

See:

- [Using packages](using-packages/README.md) for installing, updating and removing
  dependencies
- [Publishing packages](publishing/README.md) for creating and publishing your own
- [`argon-package.json` and `iso-lock.json`](package-files/README.md) for the file formats

## Configuration

Isotope stores its settings in `isotope/` inside your
[configuration directory](../standard-library/path/README.md#configuration-directory)
(`~/.config/isotope/` on Linux):

| File | Contents |
|---|---|
| `config.json` | settings, currently just `default_remote`, the default registry (`"isotope.wbell.dev"`) |
| `auth.json` | access tokens saved with `isotope auth add`, keyed by registry host |

To use your own registry by default, change `default_remote`:

```json
{
    "default_remote": "packages.example.com"
}
```

Registries are run with [isotope-server](https://git.wbell.dev/Open-Argon/isotope-server-2),
which you can self-host.
