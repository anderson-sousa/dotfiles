# AGENTS.md

Personal dotfiles for an Arch Linux + Hyprland (Wayland) desktop. There is no build. The repo holds config files that get symlinked into `$HOME`, plus a `Taskfile` for setting up the machine and checking the code.

## Layout

Everything that gets deployed lives under `dotfiles/`. The repo root only holds tooling (`Taskfile`, `.editorconfig`, `.pre-commit-config.yaml`).

| Repo path | Symlinked to |
|---|---|
| `dotfiles/zshrc` | `~/.zshrc` |
| `dotfiles/gitconfig` | `~/.gitconfig` |
| `dotfiles/ssh/config` | `~/.ssh/config` |
| `dotfiles/tool-versions` | `~/.tool-versions` (asdf: python, nodejs) |
| `dotfiles/.tmux.conf` | `~/.tmux.conf` (TPM + tokyo-night theme) |
| `dotfiles/aliases/` | `~/.aliases` |
| `dotfiles/functions/` | `~/.functions` |
| `dotfiles/config/hypr/` | `~/.config/hypr` |
| `dotfiles/config/waybar/` | `~/.config/waybar` |
| `dotfiles/config/kitty/` | `~/.config/kitty` |
| `dotfiles/local/bin/k` | `~/.local/bin/k` (fzf process killer) |
| `dotfiles/local/bin/docker_clean_images` | `~/.local/bin/dci` (renamed on link) |
| `dotfiles/local/bin/rfv` | `~/.local/bin/rfv` (ripgrep + fzf, opens nvim) |

`task:create_symlinks` in `Taskfile` is the only place this mapping is defined. It hardcodes the repo path as `$HOME/workspaces/personal/dotfiles` and uses `ln -snf`, so it overwrites existing files without making a backup. **When you add a new dotfile, also add its `ln` line there.**

## Taskfile

`Taskfile` is a bash script based on [Enrise/Taskfile](https://github.com/Enrise/Taskfile). Tasks are `function task:<name>`. Run one with `./Taskfile <name>`; `zshrc` also aliases `run` to `./Taskfile`.

- `./Taskfile help` lists only the tasks that carry a `## description` comment. Most install tasks have none, so they don't show up in the list.
- Setup tasks: `create_symlinks`, `install_*` (pacman/yay package groups), `install_yay`, `install_oh_my_zsh` (also clones p10k and the zsh plugins). They change the system and some need `sudo`. Don't run them unless asked.
- `./Taskfile lint` runs `shellcheck` on every file that `file` reports as a shell script, skipping `.git`, `.history/` and gitignored files. It returns non-zero if any file fails.
- `./Taskfile format` formats shell files with `shfmt -w` and `*.json`/`*.jsonc` files with `npx prettier --trailing-comma none`.
- `validate_code` is a stub; it doesn't check the Hyprland Lua config yet.
- `shellcheck` and `shfmt` aren't installed by any task, so they must already be on the system.

The Taskfile runs with `set -eo pipefail`. Its helpers (`title`, the color variables, `run-task`) are defined below the tasks, which works because they're only called at the bottom of the file.

## Conventions

- `.editorconfig`: LF line endings, 2-space indent, UTF-8, final newline, trim trailing whitespace. The Hyprland Lua file uses 4 spaces in practice; stay consistent with the file you're editing.
- Shell scripts start with `#!/usr/bin/env bash` (or `#!/bin/bash`) and must pass shellcheck. Scripts in `local/bin/` start with a short comment saying what they do.
- Alias files are named `aliases/*_aliases.zsh`. `zshrc` sources everything in `~/.functions/*` and `~/.aliases/*` automatically, so a new file there needs no other wiring.
- Commit messages use the Conventional Commits style (`feat:`, `build:`, ...).
- A pre-commit hook runs **gitleaks**. Never commit secrets, keys or tokens. `ssh/config` and `gitconfig` are already tracked, so keep secrets out of them.

## Hyprland / Waybar specifics

- `config/hypr/hyprland.lua` uses Hyprland's **Lua config API** (`hl.monitor`, `hl.bind`, `hl.dsp.*`, `hl.window_rule`, ...), not the older `hyprland.conf` syntax. Match the existing API when editing.
- The setup has three 4K monitors at scale 1.6: `DP-3` (left, workspaces 1–3), `DP-2` (center, 4–7) and `DP-4` (right, 8–10). These output names appear in **three places that must stay in sync**:
  - the `hl.monitor` and `hl.workspace_rule` calls in `hyprland.lua`
  - `persistent-workspaces` in `config/waybar/config.jsonc`
  - the `wallpaper` blocks in `config/hypr/hyprpaper.conf`. Wallpapers load from a NAS mount at `~/TRUENAS/...`.
- Keyboard layout is `br` (ABNT2). The main modifier is `SUPER`, with vim-style `h/j/k/l` bindings for focus, move (`+SHIFT`), resize (`+CTRL`) and window cycling (`+ALT`).
- Hyprland autostarts `hyprpaper`, `waybar` and the `copyq` server. `SUPER+SHIFT+W` restarts waybar.

## Kitty

`config/kitty/themes/` is a vendored set of about 180 third-party color themes; don't edit those files. To change the theme, switch the `include ./themes/<name>.conf` line in `kitty.conf`.

## Ignore

`.history/` holds the VS Code Local History extension's snapshots. It's gitignored and excluded from lint/format, so don't read or edit it.
