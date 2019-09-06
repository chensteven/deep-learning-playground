# A guide to building a Deep Learning Environment on an Ubuntu Linux machine

## Install Vim
```
sudo apt-get remove vim-tiny
sudo apt-get install vim
```
We're going to install:
- Nvidia CUDA Toolkit 10.0
- Nvidia CUDNN 10.0
- Pyenv
- Pyenv-virtualenv
- Python
- Tensorflow

## Check if GPU is properly mounted
```
lspci | grep -i nvidia

00:04.0 3D controller: NVIDIA Corporation GK210GL [Tesla K80] (rev a1)
00:05.0 3D controller: NVIDIA Corporation GK210GL [Tesla K80] (rev a1)
```

## Update system
```
sudo apt-get update
sudo apt-get upgrade -y
```

## Install useful Linux stuff
```
sudo apt-get install -y build-essential
sudo apt-get install -y cmake
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev python-openssl git
```

## Check if NVIDIA Driver is installed
```
cat /proc/driver/nvidia/version

NVRM version: NVIDIA UNIX x86_64 Kernel Module  410.48  Thu Sep  6 06:36:33 CDT 2018
GCC version:  gcc version 7.4.0 (Ubuntu 7.4.0-1ubuntu1~18.04) 

```

## Install CUDA Toolkit
You can download the installer from: https://developer.nvidia.com/cuda-toolkit

### Installation
#### Download installer
```
cd /tmp; wget https://developer.nvidia.com/compute/cuda/10.0/Prod/local_installers/cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64
```
#### Use installer
```
sudo dpkg -i cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64.deb
sudo apt-key add /var/cuda-repo-10-0-local-10.0.130-410.48/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda -y
```
#### Set environment path
```
cat <<EOF >> ~/.bashrc
export CUDA_HOME=/usr/local/cuda-10.0
export LD_LIBRARY_PATH=\${CUDA_HOME}/lib64
export PATH=\${CUDA_HOME}/bin:\${PATH}
EOF
```
#### Re-load bash script
```
source ~/.bashrc
```
#### Verify CUDA Toolkit installation
```
nvcc -V

nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2018 NVIDIA Corporation
Built on Sat_Aug_25_21:08:01_CDT_2018
Cuda compilation tools, release 10.0, V10.0.130
```

## Install CUDNN

### Installation
#### Send the tar file to cloud from local using SSH
```
scp -i ~/.ssh/my-ssh-key cudnn-10.0-linux-x64-v7.5.1.10.tgz [USERNAME]@[EXTERNAL_IP_ADDRESS]:~
```

#### Extract the tar file
```
tar xvzf cudnn-9.2-linux-x64-v7.1.tgz
```

#### Make symlinks for /usr/local/cuda
```
sudo cp -P cuda/include/cudnn.h /usr/local/cuda/include
sudo cp -P cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

#### Verify CUDNN installation
```
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2

#define CUDNN_MAJOR 7
#define CUDNN_MINOR 5
#define CUDNN_PATCHLEVEL 1
--
#define CUDNN_VERSION (CUDNN_MAJOR * 1000 + CUDNN_MINOR * 100 + CUDNN_PATCHLEVEL)

#include "driver_types.h"
```

## Install pyenv
Pyenv is a simple Python version management

### Installation
Source: https://github.com/pyenv/pyenv

0. Install dependencies first
```
sudo apt-get update; sudo apt-get install --no-install-recommends make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev

```

1. Check out pyenv where you want it installed. A good place to choose is $HOME/.pyenv (but you can install it somewhere else).
```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```
2. Define environment variable PYENV_ROOT to point to the path where pyenv repo is cloned and add $PYENV_ROOT/bin to your $PATH for access to the pyenv command-line utility.
```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
```
3. Add pyenv init to your shell to enable shims and autocompletion. Please make sure eval "$(pyenv init -)" is placed toward the end of the shell configuration file since it manipulates PATH during the initialization.
```
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
```
4. Restart your shell so the path changes take effect. You can now begin using pyenv.
```
exec "$SHELL"
```
5. Install Python versions into $(pyenv root)/versions. For example, to download and install Python 2.7.8, run:
```
pyenv install 3.7.3
```

## Install pyenv-virtualenv
Pyenv plugin to manage virtualenv

### Installation
Source: https://github.com/pyenv/pyenv-virtualenv

1. Check out pyenv-virtualenv into plugin directory
```
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```
2. (OPTIONAL) Add pyenv virtualenv-init to your shell to enable auto-activation of virtualenvs. This is entirely optional but pretty useful. See "Activate virtualenv" below.
```
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
```
3. Restart your shell to enable pyenv-virtualenv
```
exec "$SHELL"
```

## Install gcloud

