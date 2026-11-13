# Installation

Chloride targets **Linux**, **macOS**, **Windows** and **FreeBSD**, in that order of
priority. Official builds are produced for:

| Platform | Architectures | Format |
|---|---|---|
| Debian / Ubuntu | x86_64, arm64 | `.deb` (apt repository) |
| Fedora / RHEL | x86_64, arm64 | `.rpm` (dnf repository) |
| macOS | arm64, x86_64 | Homebrew tap |
| Windows | x86_64 | setup executable |
| Docker | x86_64, arm64 | `git.wbell.dev/open-argon/argon` image |

Every installation includes:

- `argon`: the interpreter
- `isotope`: the [package manager](../package-manager/README.md)
- the [standard library](../standard-library/README.md)
- the C headers (`Argon.h`, `ArgonFunction.h`, `ArgonTypes.h`) for
  [native extensions](../tooling/native-extensions/README.md)

If your platform isn't officially supported, feel free to open an issue, or build from
source (see [Building from source](../tooling/building-from-source/README.md)) and send a
pull request.

## Debian (and Debian-based distributions)

Add the Open-Argon apt repository. Packages are published for the `trixie` and `bookworm`
distributions (and `bullseye` on x86_64). Replace `trixie` below with your release if needed:

```bash
sudo curl https://git.wbell.dev/api/packages/Open-Argon/debian/repository.key -o /etc/apt/keyrings/gitea-Open-Argon.asc
echo "deb [signed-by=/etc/apt/keyrings/gitea-Open-Argon.asc] https://git.wbell.dev/api/packages/Open-Argon/debian trixie main" | sudo tee -a /etc/apt/sources.list.d/gitea.list
sudo apt update
```

Then install:

```bash
sudo apt install argon
```

## Fedora (and Fedora-based distributions)

Import the signing key:

```bash
curl https://git.wbell.dev/Open-Argon/Chloride/raw/branch/main/gpg/argon-packages-public.gpg | sudo rpm --import -
```

Add the repository:

```bash
# Fedora 41+ (DNF5)
sudo dnf config-manager addrepo --from-repofile=https://git.wbell.dev/api/packages/Open-Argon/rpm.repo

# older RedHat-based distributions (DNF4)
sudo dnf config-manager --add-repo https://git.wbell.dev/api/packages/Open-Argon/rpm.repo
```

Then install:

```bash
sudo dnf install argon
```

## macOS (Homebrew)

Homebrew is the recommended way to install on macOS, supporting both Apple Silicon and
Intel:

```bash
brew tap open-argon/argon https://git.wbell.dev/Open-Argon/homebrew-open-argon
brew install argon
```

## Windows

There's no package manager distribution yet (no winget/chocolatey), but a Windows setup
executable (`argon-<version>-windows-installer-x86_64.exe`) is built for every release.

## Docker

A multi-architecture Debian-based image is published for every release:

```bash
docker run --rm -it git.wbell.dev/open-argon/argon:latest
```

## Building from source

If none of the above work for you, see [Building from source](../tooling/building-from-source/README.md).

## Checking the installation

```bash
argon --version
```

prints the installed Chloride version (for example `v4.1.0`).

## Next steps

Once `argon` is installed, head to [Your first program](first-program/README.md).
