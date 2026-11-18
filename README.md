# dotfiles

Personal dotfiles for an Arch Linux + Hyprland (Wayland) desktop.

## What's inside

| Area | Files |
|---|---|
| Shell | `zshrc` (oh-my-zsh + p10k), `aliases/`, `functions/` |
| Desktop | `config/hypr/` (Hyprland Lua config, hyprpaper), `config/waybar/` |
| Terminal | `config/kitty/`, `.tmux.conf` (TPM + tokyo-night) |
| Git / SSH | `gitconfig`, `ssh/config` |
| Tooling | `tool-versions` (asdf: python, nodejs) |
| Scripts | `local/bin/k` (fzf process killer), `dci` (Docker image cleanup), `rfv` (ripgrep + fzf → nvim) |

Everything that gets deployed lives under `dotfiles/`.

## Install

The repo is expected at `~/workspaces/personal/dotfiles`.

```sh
git clone <repo-url> ~/workspaces/personal/dotfiles
cd ~/workspaces/personal/dotfiles

./Taskfile install_yay            # AUR helper
./Taskfile install_oh_my_zsh      # oh-my-zsh, p10k and zsh plugins
./Taskfile install_pre_commit     # pre-commit hook manager
./Taskfile create_symlinks        # link the dotfiles into $HOME
```

> `create_symlinks` uses `ln -snf` and **overwrites existing files without a backup**.

Package groups are installed with the `install_*` tasks (e.g. `install_desktop_environment`, `install_terminal_tools`, `install_docker`). See `Taskfile` for the full list.

## Development

```sh
./Taskfile help     # list documented tasks
./Taskfile lint     # shellcheck all shell scripts
./Taskfile format   # shfmt + prettier (JSON/JSONC)
```

Requires `shellcheck` and `shfmt` on the system. A pre-commit hook runs gitleaks.
