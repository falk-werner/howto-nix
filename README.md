# HowTo nix

This repository contains some information how to use nix.

## Installation

```bash
sh <(curl -L https://nixos.org/nix/install) --no-daemon
```

Depite recommended otherwise, I prefer to install nix in single user mode.
This way, `sudo` is not required to run nix

### Activate Flakes

I prefer [nix flakes](https://nixos.wiki/wiki/Flakes) over the traditional `shell.nix` to describe my development environment. Since 'flakes' is an
experimental feature, it must be activated separately. Therefore, add
the follwing line to `~/.config/nix/nix.conf`:

```ini
experimental-features = nix-command flakes
```

_Note_: You might need to re-login to make `nix` commands available.

### Flake templates

There is a variety of flake templates available at 
[https://github.com/the-nix-way/dev-templates](https://github.com/the-nix-way/dev-templates).

### Enter development shell

Once a `flake.nix` file is available in the current directory, a `nix` development
shell can be entered using the following command:

```bash
nix develop
```

_Note_: see [next section](#nix-and-direnv) to learn how to enter the `nix`
development environment automatically when the directory is entered.


## Nix and direnv

I recommend using [direnv](ttps://direnv.net/) to enter a `nix` development
environment automatically whenever the directory is entered.
Note that version `2.30` is needed for `flakes` support.

### Ubuntu 24.04

A recent version of `direnv` can be installed via `apt`in Ubuntu 24.04:

```bash
sudo apt install direnv
```

### Ubuntu 22.04 and older

The `direnv` versions of older Ubuntu versions do not support `flakes`.
Therefore, the [installer](https://direnv.net/docs/installation.html#from-binary-builds) can be used to install a recent version of
`nix`:

```bash
curl -sfL https://direnv.net/install.sh | bash
```

_Note_: The installer has some funny logic to determine where to install `direnv`
in the path.

### Add bash hook

To make `direnv` work, add the following line at the end of your
`~/.bashrc` file:

```bash
eval "$(direnv hook bash)"
```

_Note_: Make sure it appears even after rvm, git-prompt and other shell extensions that manipulate the prompt.

### Create .envrc file for nix

Since `direnv` executes a `.envrc` file, when a entering a directory, you have
to specify one such file in your project. In oder to use `flakes` the file should
contain the following line:

```bash
use flake
```

## VS Code direnv

There is a `VS Code` plugin to enable `direnv` support:

- [https://marketplace.visualstudio.com/items?itemName=mkhl.direnv](https://marketplace.visualstudio.com/items?itemName=mkhl.direnv)

Using this plugin, `VS Code` enters the `nix` development environment
on startup, so that other `VS Code` plugins can access the tools
provided by the particular development environment.

## Disadvantages

- `nix` does not provide a perfect isolation, especially in the development environment  
  When entering a development environment, `nix` respects your `.bashrc`. Therefore,
  settings made available in this file are also available in your development environment.
  Note that `nix` will prepend the `PATH` by the packages described in `flake.nix`, but
  the remainder of `PATH` is left unchanged. This way, you have access to tools, which
  are not provided by `nix`.

## References

- [nix installation](https://nixos.org/download/)
- [flakes](https://nixos.wiki/wiki/Flakes)
- [the-nix-way/dev-templates](https://github.com/the-nix-way/dev-templates)
- [direnv](https://direnv.net/)
  - [install from binary build](https://direnv.net/docs/installation.html#from-binary-builds)
  - [hook installation](https://direnv.net/docs/hook.html)
  - [VS Code plugin](https://marketplace.visualstudio.com/items?itemName=mkhl.direnv)
- [Installing a Specific Version of a Package with Nix](https://mplanchard.com/posts/installing-a-specific-version-of-a-package-with-nix.html)

