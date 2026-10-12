# linuxify

Make the macOS command line behave like GNU/Linux:

- install GNU versions of the BSD tools macOS ships (`sed`, `awk`, `grep`, `find`, `tar`, `ls`, `getopt`, ...)
- put them first on `PATH` under their usual names, so you do not need the `g` prefix
- install current versions of tools macOS ships in an old version (`bash`, `make`, `less`, `rsync`, ...)

The formula list is in [`Brewfile`](Brewfile). Tested on macOS 11 (Big Sur) and later, on Apple Silicon and Intel.

## Install

```bash
git clone https://github.com/nicnic-cc/relinuxify.git
cd relinuxify
./linuxify install
```

Then add this line to `~/.bashrc` or `~/.zshrc` and open a new terminal:

```bash
[ -f ~/.config/linuxify/env.sh ] && . ~/.config/linuxify/env.sh
```

Check the result:

```bash
./linuxify status
```

Rerunning `install` is safe; it installs whatever is missing and regenerates `env.sh`.

Source `env.sh` from `~/.bashrc` or `~/.zshrc`, not from `~/.zshenv` or another file that non-interactive shells read. Some macOS scripts expect the BSD tools, for example `sed -i ''`, `stat -f` or `date -j`.

### Formulas you already had

`install` only manages the formulas it installs. A Brewfile formula that was already installed stays yours, and `uninstall` leaves it alone. To hand those over to linuxify, so `uninstall` removes them too:

```bash
./linuxify install --adopt
```

## What install changes

Everything `install` changes is recorded in `~/.config/linuxify/` (or `$XDG_CONFIG_HOME/linuxify/`), so `uninstall` can undo it:

| Change | Record |
| --- | --- |
| installs the Brewfile formulas | `managed`: formulas linuxify installed or adopted |
| writes `env.sh`, which sets `PATH`, `MANPATH` and `INFOPATH` | the file itself |
| optionally switches your login shell to Homebrew's bash and adds it to `/etc/shells` | `state`: previous shell, whether `/etc/shells` was changed |
| on Intel Macs, adds `set startup-with-shell off` to `~/.gdbinit` for gdb | `state`: whether the line or the file was added |

`env.sh` is generated with your Homebrew prefix filled in, so it does not run `brew` when a shell starts, and sourcing it twice does not duplicate entries. It does not set `LDFLAGS`, `CPPFLAGS` or `PKG_CONFIG_PATH`.

## Uninstall

```bash
./linuxify uninstall         # preview
./linuxify uninstall --yes
```

`uninstall --yes`:

1. switches your login shell back to the one recorded at install, or to `/bin/zsh` if Homebrew's bash is your login shell and is about to be removed
2. uninstalls the formulas in `managed`, except any another installed formula still depends on
3. removes Homebrew's bash from `/etc/shells` if bash was uninstalled
4. removes the `~/.gdbinit` line, and the file if linuxify created it and it is now empty
5. lists dependencies nothing needs any more and asks before running `brew autoremove`
6. deletes `~/.config/linuxify`

It does not edit your shell config. Remove the line that sources `env.sh` yourself; `uninstall` tells you which file has it.

## Commands

| Command | What it does |
| --- | --- |
| `install [--adopt]` | install the Brewfile formulas and write `~/.config/linuxify/` |
| `uninstall [--yes]` | undo `install` (preview without `--yes`) |
| `status` | check that the GNU and Homebrew commands come first on `PATH` |
| `info` | `brew info` for every Brewfile formula |
