### Stuff that doesn't work:
- mounting stuff (smb shares, etc)
- lots of building and linking stuff (python wheels, some npm packages)
- Depending on manufacturer, android might kill your termux process. Use dontkillmyapp guides or root to stop this.

# try this one:

https://github.com/cheadrian/termux-chroot-proot-wine-box86_64

Setup Guide Here: https://medium.com/samsung-internet-dev/writing-software-using-a-phone-e71976f1f18d
----
[First, setup storage access:](https://wiki.termux.com/wiki/Termux-setup-storage)

`termux-setup-storage`
 touch .hushlogin

termux-change-repo


# Nerd Fonts
Can be installed with the termux styling side app. Then just choose a nerd font like fira mono 

### Termux - allow external apps
`value="true"; key="allow-external-apps"; file="/data/data/com.termux/files/home/.termux/termux.properties"; mkdir -p "$(dirname "$file")"; chmod 700 "$(dirname "$file")"; if ! grep -E '^'"$key"'=.*' $file &>/dev/null; then [[ -s "$file" && ! -z "$(tail -c 1 "$file")" ]] && newline=$'\n' || newline=""; echo "$newline$key=$value" >> "$file"; else sed -i'' -E 's/^'"$key"'=.*/'"$key=$value"'/' $file; fi`

# Zsh, OMZSH, P10K, etc.
```
# Core tools
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

also while youre in there make sure to uncomment the omzsh line to add `$HOME.local/bin/` to the path

# editors
ndk-multilib (compiling stuff)
chafa (yazi image previews)
poppler (yazi pdf previews)
```
pkg install imagemagick -y
pkg install build-essential -y
pkg install binutils -y 
pkg install ndk-multilib -y
pkg install ninja -y

pkg install rust -y
pkg install python -y
pkg install uv -y
pkg install nodejs -y

pkg install micro -y
apt install lazygit -y
pkg install helix-editor -y
pkg install helix-grammars -y
pkg install ffmpeg -y
pkg install git -y
pkg install openssh -y
pkg install samba -y
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

# npm note:
you can only run npm install inside the home dir. on the SD card, symlinks are broken

## Cache GH Passwords
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

# Python Stuff
Impossible to install micromamba on termux??? It's missing links to libpthread, etc.
Might be possible to patch elf file to get it running. [see here](https://pyarmor.readthedocs.io/en/latest/tutorial/installation.html#termux-issues)

Meantime you can install numpy and pandas this way:
https://github.com/termux/termux-packages/discussions/19126

### Python based packages
Python LSP for Helix
NBTerm sucks but no other alt
yt-dlp to download youtube vids
glances is htop improvement
tldr is for quick command lookups
```
pip install nbterm
pip install yt-dlp
pip install --user glances
pip install python-lsp-server[all]
pip install tldr
```
# Rust Packages
### zellij
```
cargo install --locked zellij
cargo install serpl
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

### Adding LSP to helix

they're all here: https://github.com/helix-editor/helix/wiki/Language-Server-Configurations 

markdown:
https://github.com/artempyanykh/marksman/blob/main/docs/install.md
download binary and move to /usr/bin

html
```
npm install --global vscode-html-languageserver-bin
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
