# Formulas installed by `linuxify install`.
# GNU tools that shadow a BSD tool are put first on PATH by the generated
# env.sh (see GNUBIN_FORMULAS and KEG_BIN_FORMULAS in ./linuxify).

# GNU replacements for BSD tools that ship with macOS
brew "coreutils"
brew "diffutils"
brew "ed"
brew "findutils"
brew "gawk"
brew "gnu-getopt"
brew "gnu-indent"
brew "gnu-sed"
brew "gnu-tar"
brew "gnu-which"
brew "gpatch"
brew "grep"
brew "gzip"
brew "make"

# GNU tools missing from macOS or shipped in an old version
brew "bash"
brew "less"
brew "screen"
brew "wdiff"
brew "wget"

# Other tools common on GNU/Linux distributions
brew "emacs"
brew "file-formula"
brew "git"
brew "nano"
brew "openssh"
brew "python"
brew "rsync"
brew "unzip"
brew "watch"

# gdb is only available for Intel Macs
brew "gdb" if Hardware::CPU.intel?
