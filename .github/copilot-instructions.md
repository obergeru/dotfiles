# Copilot Instructions

This is a [chezmoi](https://chezmoi.io) dotfiles repository managing shell and tool configuration for macOS and Linux.

## Applying Changes

```sh
# Apply all dotfiles to the home directory
chezmoi apply

# Preview what would change without applying
chezmoi diff

# Bootstrap a fresh machine (installs chezmoi if missing, then applies)
./bootstrap.sh

# Bootstrap from GitHub (uses obergeru as source)
./bootstrap.sh -g

# Install packages and configure Zsh plugins (run_once script)
chezmoi apply   # chezmoi runs run_once_* scripts automatically when needed
```

## chezmoi File Naming Conventions

chezmoi uses special filename prefixes/suffixes that map to actual home directory paths:

| Source name | Target path |
|---|---|
| `dot_zshrc` | `~/.zshrc` |
| `dot_Brewfile` | `~/.Brewfile` |
| `run_once_install-packages.sh.tmpl` | Runs once on `chezmoi apply` |
| `empty_starship.toml` | Creates an empty target file |
| `.tmpl` suffix | Processed as a Go template before writing |

## Architecture

- **`bootstrap.sh`** — Installs chezmoi itself and runs `chezmoi init --apply`. Entry point for new machines.
- **`run_once_install-packages.sh.tmpl`** — Runs on first `chezmoi apply` (or when its contents change). Installs Homebrew, runs `brew bundle`, sets up fzf shell integration, clones/symlinks Zsh plugins, and sets the default shell to zsh. Cross-platform: branches on `$(uname)` for Darwin vs Linux.
- **`dot_Brewfile`** — Deploys to `~/.Brewfile`. Consumed via `brew bundle --global` in the run_once script; you can also run `brew bundle --global` manually at any time.
- **`dot_zshrc`** — The main shell config. Sections are ordered deliberately (GPG → Homebrew → compinit → plugins → prompt) because ordering matters for fzf-tab and completion hooks.
- **`private_dot_config/empty_starship.toml`** — Starship prompt config at `~/.config/starship.toml`.

## Key Conventions

- **Plugin storage**: Zsh plugins live in `~/.zsh/`. On macOS they are symlinked from the Homebrew prefix; on Linux they are cloned directly from GitHub.
- **fzf-tab ordering**: `fzf-tab` must be sourced *after* `compinit` and all `zstyle` rules. `zsh-syntax-highlighting` must be sourced *last*.
- **Cross-platform guards**: Use `[[ "$(uname)" == "Darwin" ]]` in `.zshrc` and `[ "$(uname)" = "Darwin" ]` (POSIX) in shell scripts. The run_once script uses POSIX sh (`#!/bin/sh`).
- **Go templates in `.tmpl` files**: Use `{{ .chezmoi.os }}` etc. for OS-specific templating. The `run_once_install-packages.sh.tmpl` currently uses plain `uname` detection rather than chezmoi template variables.
- **`run_once_` scripts re-run trigger**: Changing the content of a `run_once_` file causes chezmoi to re-execute it on the next `chezmoi apply`. Use this intentionally — adding packages to the Brewfile alone is not enough; the run_once script must also change (or be converted to `run_onchange_`).
