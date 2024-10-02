
try this guy too:
https://github.com/jirutka/alpine-zsh-config


# making jupyter notebooks run in android 13+
https://josesho.notion.site/Installing-Analytics-Stack-in-Termux-4a6ff82d32974db4a4de27ee5e24ba45

from here: 
https://github.com/termux/proot/issues/248

in general, it seems better to run it from alpine in proot because of all the glibc/bionic bugs

# NOTE: This was written assuming proot debian in android (aarch64)
Should work for other Aarch64 Debian as well.

## zsh + omzsh + p10k
```
apk add zsh


chsh -s $(which zsh)
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Then set in `~/.zshrc`:
```
ZSH_THEME="powerlevel10k/powerlevel10k"
```

Now **restart the shell** and go through the P10k configuration.

# Python/miniforge3

```
apt update
apt install wget python3 python3-pip vim -y
```
Proot Debian on android links to the termux python by default. This breaks miniforge when installing it.
Add this to the `.zshrc` to fix it:
```
alias python="/usr/bin/python3"
alias python3="/usr/bin/python3"
alias pip="/usr/bin/pip"
alias pip3="/usr/bin/pip"
```
Also make sure to add back `/.local/bin` to the PATH in `.zshrc`. Now run this and complete it:

```
wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-aarch64.sh
bash Miniforge3-Linux-aarch64.sh
```

# Stuff to build other stuff

NOTE: Find ways to add back: `eza, popper`

### Apt based tilities I like
```
apt install tmux ffmpeg git openssh-client samba exiftool chafa fzf ripgrep bat zoxide htop nodejs npm -y
```

### Weird utilities because of Apt

[FD from here](https://github.com/sharkdp/fd?tab=readme-ov-file#installation)
```
apt install fd-find -y
mkdir -p ~/.local/bin/
ln -s $(which fdfind) ~/.local/bin/fd
```
[Glow from here](https://github.com/charmbracelet/glow)
```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://repo.charm.sh/apt/gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/charm.gpg
echo "deb [signed-by=/etc/apt/keyrings/charm.gpg] https://repo.charm.sh/apt/ * *" | sudo tee /etc/apt/sources.list.d/charm.list
sudo apt update && sudo apt install glow
```
### Python based Utilities
Install them to the base env because they're useful.
yt-dlp to download youtube vids
glances is an htop improvement
tldr is for quick command lookups
```
pip install yt-dlp
pip install --user glances
pip install tldr
```

# Rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup update
```

## Helix
Download the prebuilt binary from GitHub.
From (July 2024):
```
wget https://github.com/helix-editor/helix/releases/download/24.07/helix-24.07-aarch64-linux.tar.xz
tar -xf helix-24.07-aarch64-linux.tar.xz
```
Move that to usr/bin.
### Adding LSP to helix
[LSP list are all here](https://github.com/helix-editor/helix/wiki/Language-Server-Configurations)
```
npm install --global vscode-html-languageserver-bin
npm install -g typescript typescript-language-server
pip install python-lsp-server
```
[For markdown:](https://github.com/artempyanykh/marksman/blob/main/docs/install.md)
(Place the binary somewhere in your PATH)
```
wget https://github.com/artempyanykh/marksman/releases/download/2023-12-09/marksman-linux-arm64
mv marksman-linux-arm64 marksman && chmod +x marksman
mv marksman $HOME/.local/bin/
```

## Rust Packages
```
cargo install --locked yazi-fm yazi-cli
cargo install --locked zellij
cargo install eza
```
[Yazi from Here](https://yazi-rs.github.io/docs/installation#official-binaries)
Remember to install a nerd font for Yazi. It should have come from P10k already
```
cargo install --locked yazi-fm yazi-cli
```

# Stuff to add to the `.zshrc`
also make sure `/.local/bin` is in the path!!
```
export EDITOR="hx"

alias ls="eza"
alias python="/usr/bin/python3"
alias python3="/usr/bin/python3"
alias pip="/usr/bin/pip"
alias pip3="/usr/bin/pip"

# zz alias for zellij
alias zz="zellij"

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

### Add Eza completions to zsh:
[Follow it from here](https://github.com/eza-community/eza/blob/main/INSTALL.md#completions)



### Yazi  config
```
echo `
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

[tasks]
micro_workers    = 3
macro_workers    = 10
bizarre_retry    = 2
image_alloc      = 67108864  # 64MB
image_bound      = [ 0, 0 ]
suppress_preload = false
' > ~/.config/yazi/yazi.toml
```

# If rooted
### Earlyroom (oom killer)
```
sudo apt install earlyroom
systemctl enable --now earlyoom 
```

