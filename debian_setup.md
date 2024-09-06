install miniforge
https://ivonblog.com/en-us/posts/android-termux-anaconda/

## zsh + omzsh + p10k
```
sudo apt install zsh
chsh -s $(which zsh)
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Then set in `~/.zshrc`:
```
ZSH_THEME="powerlevel10k/powerlevel10k"
```

# Stuff to build other stuff
### NPM
```
apt install nodejs npm
```

### Rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup update
```

MISSING FD, glow, eza, popper, IN HERE

### Utilities I like
```
apt install tmux ffmpeg git openssh-client samba exiftool chafa fzf ripgrep bat zoxide htop -y
```

# Helix
Download the prevuolt binaries from GitHub. move them to usr/bin 

## Rust Packages
```
cargo install --locked yazi-fm yazi-cli
cargo install --locked zellij
cargo install serpl
```

### Python based packages
Python LSP for Helix
NBTerm sucks but no other alt
yt-dlp to download youtube vids
glances is htop improvement
tldr is for quick command lookups
```
pip install yt-dlp
pip install --user glances
pip install tldr
```

### Adding LSP to helix
they're all here: https://github.com/helix-editor/helix/wiki/Language-Server-Configurations 
markdown:
https://github.com/artempyanykh/marksman/blob/main/docs/install.md
brew install marksman
html
```
npm install --global vscode-html-languageserver-bin
npm install -g typescript typescript-language-server
pip install python-lsp-server[all]
```



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
# Aliases in zshrc
alias zz="zellij"
alias ls="eza"
add the python aliases from ivonblog
also add back use bin to path in zshrc


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

# Tmux config (ugh)
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

# (if rooted) Earlyroom (oom killer)
```
sudo apt install earlyroom
systemctl enable --now earlyoom 
```

