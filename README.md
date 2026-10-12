<p align="center"><img src="assets/icon.svg" width="128" alt="relinuxify icon"></p>

# linuxify

Make the macOS command line behave like GNU/Linux. linuxify installs the GNU versions of the BSD tools macOS ships (`sed`, `awk`, `grep`, `find`, `tar`, `ls`, ...) and puts them first on `PATH` under their usual names, so you don't need the `g` prefix. It also installs current versions of tools macOS ships old, such as `bash` and `make`.

Fork of [pkill37/linuxify](https://github.com/pkill37/linuxify), rewritten. Tested on Apple Silicon. Intel should work but is untested.

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

You can rerun `install`: it installs what's missing and regenerates `env.sh`.

Source `env.sh` from `~/.bashrc` or `~/.zshrc` only, never from `~/.zshenv` or another file non-interactive shells read. Some macOS scripts call `sed -i ''`, `stat -f` or `date -j` and break with the GNU versions.

## Commands

| Command | What it does |
| --- | --- |
| `install [--adopt]` | install the Brewfile formulas and write `~/.config/linuxify/` |
| `uninstall [--yes]` | undo `install` (preview without `--yes`) |
| `status` | check that the GNU and Homebrew commands come first on `PATH` |
| `info` | `brew info` for every Brewfile formula |

## What gets installed

The list lives in [`Brewfile`](Brewfile).

GNU replacements for the BSD tools in macOS. These take over the plain command names:

| Formula | Commands | Replaces |
| --- | --- | --- |
| coreutils | `ls` `cp` `mv` `rm` `date` `stat` `sort` `du` `df` ... | BSD versions |
| diffutils | `diff` `cmp` `diff3` `sdiff` | BSD versions |
| ed | `ed` | BSD `ed` |
| findutils | `find` `xargs` `locate` | BSD versions |
| gawk | `awk` | BSD `awk` |
| gnu-getopt | `getopt` (long options) | BSD `getopt` |
| gnu-indent | `indent` | BSD `indent` |
| gnu-sed | `sed` | BSD `sed` |
| gnu-tar | `tar` | bsdtar |
| gnu-which | `which` | BSD `which` |
| gpatch | `patch` | BSD `patch` |
| grep | `grep` `egrep` `fgrep` | BSD `grep` |
| gzip | `gzip` `gunzip` `zcat` | macOS `gzip` |
| make | `make` | GNU make 3.81 |

Despite its name, gnu-getopt is from util-linux.

Newer GNU tools: bash, less, screen, wdiff, wget.

Other tools common on Linux: emacs, file, git, nano, openssh, python, rsync, unzip, watch, and gdb on Intel Macs only.

## What install changes

`install` records everything it changes in `~/.config/linuxify/` (or `$XDG_CONFIG_HOME/linuxify/`), so `uninstall` can undo it:

| Change | Record |
| --- | --- |
| installs the Brewfile formulas | `managed`: formulas linuxify installed or adopted |
| writes `env.sh`, which sets `PATH`, `MANPATH` and `INFOPATH` | the file itself |
| optionally switches your login shell to Homebrew's bash and adds it to `/etc/shells` | `state`: previous shell, whether `/etc/shells` was changed |
| on Intel Macs, adds `set startup-with-shell off` to `~/.gdbinit` for gdb | `state`: whether the line or the file was added |

`env.sh` has your Homebrew prefix filled in, so a new shell doesn't run `brew`, and sourcing it twice doesn't duplicate `PATH` entries.

### Formulas you already had

`uninstall` removes only the formulas `install` installed. To hand over Brewfile formulas you already had, so `uninstall` removes them too:

```bash
./linuxify install --adopt
```

## Uninstall

```bash
./linuxify uninstall         # preview
./linuxify uninstall --yes
```

`uninstall --yes`:

1. Switches your login shell back to the one recorded at install, or to `/bin/zsh` if Homebrew's bash is your login shell and is about to be removed
2. Uninstalls the formulas in `managed`, except those another installed formula still depends on
3. Removes Homebrew's bash from `/etc/shells` if bash was uninstalled
4. Removes the `~/.gdbinit` line, and the file if linuxify created it and it is now empty
5. Lists dependencies nothing needs any more and asks before running `brew autoremove`
6. Deletes `~/.config/linuxify`

It does not edit your shell config. Remove the line that sources `env.sh` yourself; `uninstall` tells you which file has it.
