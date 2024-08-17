MacOs

### mamba python
curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
bash Miniforge3-$(uname)-$(uname -m).sh
~/miniconda3/bin/conda init zsh

## JAX with metal acceleration
### See this: https://developer.apple.com/metal/jax/
--> Doesn't work (good job apple!)