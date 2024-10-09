### Stuff that doesn't work:
- mounting stuff (smb shares, etc)
- lots of building and linking stuff (python wheels, some npm packages)
- Depending on manufacturer, android might kill your termux process. Use dontkillmyapp guides or root to stop this.


## TODO:
add euporie as default opener in yazi

---
# Termux Setup

### Termux - allow external apps
`value="true"; key="allow-external-apps"; file="/data/data/com.termux/files/home/.termux/termux.properties"; mkdir -p "$(dirname "$file")"; chmod 700 "$(dirname "$file")"; if ! grep -E '^'"$key"'=.*' $file &>/dev/null; then [[ -s "$file" && ! -z "$(tail -c 1 "$file")" ]] && newline=$'\n' || newline=""; echo "$newline$key=$value" >> "$file"; else sed -i'' -E 's/^'"$key"'=.*/'"$key=$value"'/' $file; fi`

[Setup storage access:](https://wiki.termux.com/wiki/Termux-setup-storage)
```
termux-setup-storage
touch .hushlogin
termux-change-repo
```

---
## Basic shell env

Note that Nerd Fonts can be installed with the termux styling side app. P10k will also install them.


# Zsh, OMZSH, P10K, etc.
```
pkg install zsh -y
chsh -s $(which zsh)
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### P10k
```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
Then set in `~/.zshrc`:
```
ZSH_THEME="powerlevel10k/powerlevel10k"
```
also while youre in there make sure to uncomment the omzsh line to add `$HOME.local/bin/` to the path.
Now, exit and restart termux.
------------
# Basic Dev environment
```
pkg update -y && pkg upgrade -y
kg install -y git clang llvm python libzmq openssh samba ffmpeg
pkg install build-essential -y
pkg install binutils -y
pkg install -y binutils-is-llvm
pkg install ndk-multilib -y
pkg install libandroid-execinfo -y
pkg install ninja -y
pkg install -y cmake libcrypt make
pkg install dnsutils jq -y
pkg install imagemagick -y

pkg install rust -y
pkg install python -y
pkg install uv -y
pkg install nodejs -y

pkg install micro -y
pkg install helix-editor -y
pkg install helix-grammars -y

apt install lazygit -y
pkg install exiftool -y
pkg install chafa -y
pkg install fzf -y
pkg install fd  -y
pkg install glow -y
pkg install ripgrep -y
pkg install bat -y
pkg install zoxide -y
pkg install eza -y
pkg install poppler -y
pkg install htop -y

pkg install yazi -y
pkg install zellij -y
```

---
# Python datascience environment

[need maturin for a bunch of libs](https://old.reddit.com/r/termux/comments/19c8rpg/how_to_install_maturin_and_wheel)
```
CARGO_BUILD_TARGET="$(rustc -Vv | grep "host" | awk '{print $2}')"
pip install maturin
```

Building python pip packages can be a pain. See the [tricks here](https://wiki.termux.com/wiki/Python)

DS libraries that are precompiled for termux:

```
pkg install -y python-pyarrow python-numpy matplotlib
```

[Building pandas:](https://github.com/termux/termux-packages/discussions/19126)
```
yes | pkg upgrade
pkg install python build-essential cmake ninja libopenblas libandroid-execinfo patchelf binutils-is-llvm
pip3 install setuptools wheel packaging pyproject_metadata cython meson-python versioneer

MATHLIB=m LDFLAGS="-lpython3.12" pip3 install --no-build-isolation --no-cache-dir numpy

LDFLAGS="-lpython3.12" pip3 install --no-build-isolation --no-cache-dir pandas
```


[this one for jupyter and euporie in termux](https://old.reddit.com/r/termux/comments/149b9xx/jupyter_notebook_in_termux)
```
pkg install -y libzmq
LDFLAGS=" -lm -lcompiler_rt" pip3 install jupyter
pip install --user euporie
```

# Other Packages

### Python based packages
Python LSP for Helix
NBTerm sucks but no other alt
yt-dlp to download youtube vids
glances is htop improvement
tldr is for quick command lookups
```
pip install yt-dlp
pip install python-lsp-server[all]
pip install tldr
```
# Rust Packages
### zellij
```
cargo install serpl
```
### Adding LSP to helix

[they're all here](https://github.com/helix-editor/helix/wiki/Language-Server-Configurations)

markdown:
https://github.com/artempyanykh/marksman/blob/main/docs/install.md
download binary and move to /usr/bin
html:
```
npm install --global vscode-html-languageserver-bin
```


# More Configurations

### SSH Note:
The config file might be in `~/.ssh/.config` or in `/usr/etc/ssh/ssh_config`. Make sure with `ssh -vvv ADDR_TO_CONNECT` that you're using the right one

### npm note:
you can only run npm install inside the home dir. on the SD card the symlinks are broken.

### Cache GH Passwords
```
 git config --global credential.helper 'cache --timeout=3600' 
```
----

# ZSHRC file
Includes the `yy` command for yazi and zz alias for zellij
Export config options to `.zshrc` file:
```
# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
# Initialization code that may require console input (password prompts, [y/n]
# confirmations, etc.) must go above this block; everything else may go below.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

# If you come from bash you might have to change your $PATH.
export PATH=$HOME/bin:/usr/local/bin:$PATH

# Path to your oh-my-zsh installation.
export ZSH="$HOME/.oh-my-zsh"

# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
# ZSH_THEME="robbyrussell"
ZSH_THEME="powerlevel10k/powerlevel10k"


function yy() {
	local tmp="$(mktemp -t "yazi-cwd.XXXXXX")"
	yazi "$@" --cwd-file="$tmp"
	if cwd="$(cat -- "$tmp")" && [ -n "$cwd" ] && [ "$cwd" != "$PWD" ]; then
		cd -- "$cwd"
	fi
	rm -f -- "$tmp"
}


alias zz="zellij"

plugins=(git)

source $ZSH/oh-my-zsh.sh

# User configuration

# Preferred editor for local and remote sessions
if [[ -n $SSH_CONNECTION ]]; then
  export EDITOR='vim'
else
  export EDITOR='mvim'
fi

# Compilation flags
# export ARCHFLAGS="-arch x86_64"

# Set personal aliases, overriding those provided by oh-my-zsh libs,
# plugins, and themes. Aliases can be placed here, though oh-my-zsh
# users are encouraged to define aliases within the ZSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
# alias zshconfig="mate ~/.zshrc"
# alias ohmyzsh="mate ~/.oh-my-zsh"

export PATH="/opt/homebrew/opt/node@20/bin:$PATH"

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/Users/mranger/miniforge3/bin/conda' 'shell.zsh' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/Users/mranger/miniforge3/etc/profile.d/conda.sh" ]; then
        . "/Users/mranger/miniforge3/etc/profile.d/conda.sh"
    else
        export PATH="/Users/mranger/miniforge3/bin:$PATH"
    fi
fi
unset __conda_setup

if [ -f "/Users/mranger/miniforge3/etc/profile.d/mamba.sh" ]; then
    . "/Users/mranger/miniforge3/etc/profile.d/mamba.sh"
fi
# <<< conda initialize <<<

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
```

### Yazi  config
Remember to install a nerd font!!!
micro ~/.config/yazi/yazi.toml
```
[manager]
ratio          = [ 0, 4, 4]
sort_by        = "modified"
sort_reverse   = true
mouse_events   = ["click", "scroll", "touch", "move", "drag"]

[preview]
tab_size        = 2
max_width       = 600
max_height      = 900
image_filter    = "nearest"
image_quality   = 50
sixel_fraction  = 15
ueberzug_scale  = 1
ueberzug_offset = [ 0, 0, 0, 0 ]

[opener]
open_json = [
	{run = 'fx "$@"', block=true}
]

[open]
rules = [
	{ name = "*.json", use=['open_json', 'edit', 'reveal']},
]

[tasks]
micro_workers    = 3
macro_workers    = 10
bizarre_retry    = 2
image_alloc      = 67108864  # 64MB
image_bound      = [ 0, 0 ]
suppress_preload = false
```


### Helix Config
```
theme = "default"

[editor]
cursorline = true
color-modes = true

[editor.soft-wrap]
enable = true
max-wrap = 25 
max-indent-retain = 2

[editor.cursor-shape]
insert = "bar"
normal = "block"
select = "underline"

[editor.indent-guides]
render = true
```

## Helix Languages Config
Change the config in `~/.config/helix/languages.toml`:

```
[[language]]
name = "python"
scope = "source.python"
injection-regex = "py(thon)?"
file-types = ["py", "pyi", "py3", "pyw", "ptl", "rpy", "cpy", "ipy", "pyt", { glob = ".python_history" }, { glob = ".pythonstartup" }, { glob = ".pythonrc" }, { glob = "SConstruct" }, { glob = "SConscript" }]
shebangs = ["python"]
roots = ["pyproject.toml", "setup.py", "poetry.lock", "pyrightconfig.json", ".git"]
comment-token = "#"
language-servers = [ "pylsp" ]
# TODO: pyls needs utf-8 offsets
indent = { tab-width = 4, unit = "    " }
```


## Tmux 
add zsh to tmux defaults
Add yazi image preview to it too:
https://yazi-rs.github.io/docs/image-preview#tmux

```
set -g escape-time 10
set -g mouse on 
set -g mouse-select-pane on
set -g allow-passthrough on
set -ga update-environment TERM
set -ga update-environment TERM_PROGRAM
```
new panes in same path:
`micro ~/.tmux.conf`
```
set -g mouse on
bind  c  new-window      -c "#{pane_current_path}"
bind  %  split-window -h -c "#{pane_current_path}"
bind '"' split-window -v -c "#{pane_current_path}"
```
