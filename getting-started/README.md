# Installation

Chloride mainly targets **Linux**, **macOS**, **Windows**, and **FreeBSD**, in that order of
priority. Official builds are produced for Linux x86_64, Windows x86_64, and macOS arm64.

If your platform isn't officially supported, feel free to open an issue, or build from
source (see [Building from source](../tooling/building-from-source/README.md)) and send a
pull request.

## Debian (and Debian-based distributions)

Add the Open-Argon apt repository:

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
# on RedHat based distributions
dnf config-manager --add-repo https://git.wbell.dev/api/packages/Open-Argon/rpm.repo

# Fedora 41+ (DNF5)
dnf config-manager addrepo --from-repofile=https://git.wbell.dev/api/packages/Open-Argon/rpm.repo
```

Then install:

```bash
sudo dnf install argon
```

## Windows

There's no package manager distribution yet (no winget/chocolatey), but a Windows setup
executable is built for every release.

## macOS (Homebrew)

Homebrew is the recommended way to install on macOS, supporting both Apple Silicon and
Intel:

```bash
brew tap open-argon/argon https://git.wbell.dev/Open-Argon/homebrew-open-argon
brew install argon
```

## Building from source

If none of the above work for you, see [Building from source](../tooling/building-from-source/README.md).

## Next steps

Once `argon` is installed, head to [Your first program](first-program/README.md).