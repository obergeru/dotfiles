# dotfiles

Personal dotfiles managed with [chezmoi](https://chezmoi.io). Supports macOS and Linux.

## Quick start

```sh
# Bootstrap a new machine from GitHub
sh -c "$(curl -fsLS https://chezmoi.io/get)" -- init --apply obergeru

# Or clone locally and apply
git clone https://github.com/obergeru/dotfiles ~/.local/share/chezmoi
~/.local/share/chezmoi/bootstrap.sh
```

## Usage

```sh
# Apply changes to home directory
chezmoi apply

# Preview what would change
chezmoi diff

# Edit a managed file
chezmoi edit ~/.zshrc
```

## What's included

| File | Target | Purpose |
|---|---|---|
| `dot_zshrc.tmpl` | `~/.zshrc` | Zsh config (GPG, Homebrew, completions, plugins, prompt) |
| `dot_Brewfile` | `~/.Brewfile` | Homebrew packages and casks |
| `run_once_install-packages.sh.tmpl` | *(runs once)* | Bootstrap script: installs Homebrew, plugins, sets default shell |
| `private_dot_config/empty_starship.toml` | `~/.config/starship.toml` | Starship prompt config (empty, managed by chezmoi) |
| `.chezmoi.toml.tmpl` | `~/.config/chezmoi/chezmoi.toml` | Prompts for git name/email on first run |

## Tools installed via Homebrew

- **Shell**: `zsh-autosuggestions`, `zsh-syntax-highlighting`, `fzf`, `starship`
- **Kubernetes**: `kubectl`, `kubectx`
- **Cloud**: `azure-cli`
- **Utils**: `grc`, `nerdfetch`
- **Apps**: `iterm2`, `copilot-cli`
- **Fonts**: Fira Code Nerd Font

## Zsh plugins

Plugins live in `~/.zsh/`. On macOS they are symlinked from the Homebrew prefix; on Linux they are cloned from GitHub.

| Plugin | Source |
|---|---|
| `zsh-autosuggestions` | Homebrew (macOS) / GitHub (Linux) |
| `zsh-syntax-highlighting` | Homebrew (macOS) / GitHub (Linux) |
| `fzf-tab` | GitHub (both platforms) |

> **Load order matters**: `fzf-tab` must be sourced after `compinit` and all `zstyle` rules. `zsh-syntax-highlighting` must be sourced last.
