# Make a Mojave VM for VMware:

## Start with virtualbox

1. Make a safe installation of Mojave with this script on VirtualBox: https://github.com/myspaghetti/macos-virtualbox (run this script with [Cygwin Terminal](https://cygwin.com/install.html) and don't forget to add [dependencies](https://github.com/myspaghetti/macos-virtualbox#dependencies) during the installation of Cygwin)
2. Export for VMware.

## Continue with VMware:

1. Unlock the macos on VMware: https://github.com/DrDonk/unlocker
2. Import the VM on VMware.
3. Change the screen resolution: https://github.com/MarLoe/VMware.PreferencePane
4. Fix keyboard configuration: https://github.com/pqrs-org/Karabiner-Elements
5. Install VMware tools: https://github.com/DrDonk/unlocker#6-vmware-tools

# Configuration

> Change your default shell to /bin/zsh by running the `chsh -s /bin/zsh` command.  
> **Source:** https://stackoverflow.com/a/13476258/

`.zshrc` with hstr/history configuration :

```zsh
# HSTR configuration - add this to ~/.zshrc
alias hh=hstr                    # hh to be alias forstory -r
setopt histignorespace           # skip cmds w/ leading space from history
setopt histignorealldups         # no duplicate
setopt share_history
export HSTR_CONFIG=monochromatic
#export HSTR_CONFIG=hicolor       # get more colors
bindkey -s "\C-r" "\C-a hstr -- \C-j"     # bind hstr to Ctrl-r (for Vi mode check doc)
export HISTFILE=~/.zsh_history
export HISTSIZE=10000
export SAVEHIST=${HISTSIZE}
export PROMPT_COMMAND="history -a; history -n; ${PROMPT_COMMAND}"
```

## Useful tools:

 1. [HSTR](https://github.com/dvorka/hstr) (command history viewer)
 2. [norminette](https://github.com/42School/norminette)

## Dev tools: 
 - [Sublime Text](https://www.sublimetext.com/)
 - [Sublime Merge](https://www.sublimemerge.com/)
 - [Clockify](https://github.com/clockify/browser-extension)

### Plugin for Sublime Text:
 - [42 Headers](https://packagecontrol.io/packages/42%20Headers)
 - [Linter 42-norminette](https://packagecontrol.io/packages/SublimeLinter-contrib-42-norminette)
 - my [42 tools](https://github.com/GlaceCoding/sublime-42-tools)
