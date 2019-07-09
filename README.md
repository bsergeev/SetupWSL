# SetupWSL
Instructions for setting up Windows Subsystem for Linux (WSL) for C++ development  

0. Add "set number" and "set nocompatible" to .vimrc:
```shell
vi ~/.vimrc
```
    
1. Add `PS1="\[\e[33;1m\]\w> \[\e[0m\]"` and any aliases to _.bashrc_:
```shell
vi ~/.bashrc
source ~/.bashrc
```

2. Install GCC
Install GCC prerequisites and GCC version you like (update the number in `apt install` as needed)
```shell
sudo apt update && sudo apt upgrade -y && sudo apt install build-essential gcc -y
gcc --version
sudo add-apt-repository ppa:ubuntu-toolchain-r/test && sudo apt-get update && sudo apt install gcc-8 g++-8 -y
g++ --version
```

3. Setup alternatives for GCC
```shell
sudo update-alternatives --remove-all gcc
sudo update-alternatives --remove-all g++
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 50
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 60
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 50
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-8 60
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 70
sudo update-alternatives --set cc /usr/bin/gcc
sudo update-alternatives --set c++ /usr/bin/g++
sudo update-alternatives --config gcc
sudo update-alternatives --config g++
```

4. Create GCC version switching script  
Paste the following into some file, e.g. _~/sw_gcc.sh_:
```shell
#!/bin/sh
if [ -z "$1" ]; then
    echo "usage: $0 version" 1>&2
    exit 1
fi
if [ ! -f "/usr/bin/gcc-$1" ] || [ ! -f "/usr/bin/g++-$1" ]; then
    echo "no such version gcc/g++ installed" 1>&2
    exit 1
fi
update-alternatives --set gcc "/usr/bin/gcc-$1"
update-alternatives --set g++ "/usr/bin/g++-$1" 
```
Then turn on its executable flag:
```shell
chmod +x sw_gcc.sh
```
To switch the current GCC version, e.g. to 7, run:  
```shell
sudo ~/sw_gcc.sh 7
```

5. Install _valgrind_ (check the latest _valgrind_ version and update it in the script below)
```shell
wget http://valgrind.org/downloads/valgrind-3.14.0.tar.bz2
tar -xvjf valgrind-3.14.0.tar.bz2
cd valgrind-3.14.0
./configure
make -j
make check
sudo make install
which valgrind
cd ..
rm -rf valgrind-3.14.0
rm valgrind-3.14.0.tar.bz2
```
If `valgrind --leak-check=yes ...` complains about missing non-stripped _ld.so_, run:
```shell
sudo apt-get install libc6-dbg
```
or another GCC version.

6. Instal latest CMake (check version at https://cmake.org/download/)
```shell
sudo apt remove --purge --auto-remove cmake
wget https://github.com/Kitware/CMake/releases/download/v3.13.4/cmake-3.13.4-Linux-x86_64.sh
chmod +x cmake-3.13.4-Linux-x86_64.sh
sudo ./cmake-3.13.4-Linux-x86_64.sh --skip-license --prefix=/usr/local
rm cmake-3.13.4-Linux-x86_64.sh
```

7. Install the latest nvm -> node.js (check the latest version of nvm at: https://github.com/creationix/nvm/releases)
```shell
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh >> install.sh
chmod +x install.sh
# Not sure if sudo is needed:
sudo ./install.sh
# If used sudo, change owner of .nvm:
sudo chown boris:boris ~/.nvm -R
source ~/.bashrc
nvm ls-remote
```
Install the required node.js version
```shell
nvm install 10.15.1
```

8. Install latest Boost (currently, 1.70.0)
```shell
wget -O boost_1_70_0.tar.gz https://sourceforge.net/projects/boost/files/boost/1.70.0/boost_1_70_0.tar.gz/download
tar xzvf boost_1_70_0.tar.gz
cd boost_1_70_0/
./bootstrap.sh --prefix=/usr/local
./b2 -j 4
sudo ./b2 install
```

9. Install Eigen3 library
```shell
sudo apt install libeigen3-dev
```