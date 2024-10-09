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
kg install -y git clang llvm python libzmq openssh samba
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
pkg install ffmpeg -y
pkg install exiftool -y
pkg install chafa -y
pkg install yazi -y
pkg install fzf -y
pkg install fd  -y
pkg install glow -y
pkg install ripgrep -y
pkg install bat -y
pkg install zoxide -y
pkg install eza -y
pkg install poppler -y
pkg install htop -y
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
cargo install --locked zellij
cargo install serpl
```
# NPM Packages
```
npm install --global vscode-html-languageserver-bin
```
### Adding LSP to helix

they're all here: https://github.com/helix-editor/helix/wiki/Language-Server-Configurations 

markdown:
https://github.com/artempyanykh/marksman/blob/main/docs/install.md
download binary and move to /usr/bin


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


## yy command yazi
cd $ZSH_CUSTOM
Export config options to `.zshrc` file:
```
export EDITOR="hx"

# make `yy` alias for yazi
function yy() {
	local tmp="$(mktemp -t "yazi-cwd.XXXXXX")"
	yazi "$@" --cwd-file="$tmp"
	if cwd="$(cat -- "$tmp")" && [ -n "$cwd" ] && [ "$cwd" != "$PWD" ]; then
		cd -- "$cwd"
	fi
	rm -f -- "$tmp"
}
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
theme = "github_light_high_contrast"

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
`micro ~/.config/helix/languages.toml`
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
