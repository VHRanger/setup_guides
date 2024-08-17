


### Earlyroom (oom killer)
sudo apt-get install earlyroom
systemctl enable --now earlyoom 

## zsh + omzsh
sudo apt install zsh
chsh -s $(which zsh)
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

## tmux
sudo apt-get tmux

#### add zsh to tmux defaults

### btop
sudo snap install btop

### yazi
First get rust:
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup update
```

# Helix

## micro
curl https://getmic.ro | bash
sudo mv micro /usr/bin

### mamba python
curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
bash Miniforge3-$(uname)-$(uname -m).sh
~/miniconda3/bin/conda init zsh

