# Isotope (Argon package manager)

`isotope <args>` is the same as `argon isotope <args>`. Default registry:
`isotope.wbell.dev` (browse `https://isotope.wbell.dev/packages`).

## Commands

| Command | Effect |
|---|---|
| `isotope init` | create `argon-package.json` (name = directory name, version `1.0.0`) and `iso-lock.json` |
| `isotope install` | install every dependency in `argon-package.json` (use after cloning) |
| `isotope install pkg [pkg@1.2.0 ...] [--remote host] [-g]` | install packages and record them as dependencies |
| `isotope update [pkg ...]` | update to the latest versions |
| `isotope remove pkg ...` | uninstall and remove from dependencies |
| `isotope package build` | create `__isotope__/<name>@<version>.tar.gz` |
| `isotope package push [--latest]` | publish (pre-release without `--latest`) |
| `isotope auth add <host> <token>` / `auth remove <host>` | registry tokens |
| `isotope version` / `isotope help` | |

Packages install into `./argon_modules/<name>/` (their own dependencies are nested
`argon_modules/`). `import "<name>"` finds them from any file in the project, because
imports search `argon_modules` in every parent directory. `-g` targets the installation's
global `argon_modules` (but `remove -g` / `update -g` are broken in 1.0.0).

Output contains ANSI spinner codes; when parsing it, strip escape sequences. The commands
are interactive-safe and need no prompts.

## Files

`argon-package.json`:

```json
{
    "name": "my-package",
    "version": "1.0.0",
    "dependencies": {"markdown": "0.0.1"},
    "build": "install.ar"
}
```

`build` is optional: an Argon script that runs at install time, with the unpacked archive
as the working directory, and is then deleted. It's normally used to compile native code
with the `build` module, so don't call the script `build.ar`.

`iso-lock.json`: `{"packages": {"name@version": {"remote", "url", "dependencies"}}}`.
Managed by Isotope. Commit both files; gitignore `argon_modules/`, `__isotope__/` and
`__arcache__/`.

## Package layout for publishing

```
my-package/
├── argon-package.json
├── iso-lock.json
├── LICENSE            (included automatically if present)
└── src/
    └── init.ar        (entry point; the contents of src/ become the installed package)
```

Names are 3 to 32 characters of `[a-z0-9.-]`, starting and ending alphanumeric. Versions
are at most 16 characters. Publishing needs an account on the registry, a repository
created for the package, and `isotope auth add <host> <token>`.

Settings live in `<config dir>/isotope/config.json` (`default_remote`) and `auth.json`
(`~/.config/isotope/` on Linux).
