---
layout: post
title: Installing the Spike RISC-V Simulator
description: How to install Spike on a Linux distro
summary: Installing Spike is fun!
tags: [riscv, linux]
---

Spike is a RISC-V Simulator. Here are the steps to installing Spike from source for 32bit simulation.

Dependencies:
 - Device Tree Compiler

```commandline
# Arch Linux
sudo pacman -S dtc

# Ubuntu / Debian
sudo apt install device-tree-compiler
```

Installing Spike

Make a new directory for Spike
```
mkdir RISCV
cd RISCV
```

Clone the repos for the RISCV GNU toolchain, proxy kernel (pk), and Spike itself
```
git clone --recursive https://github.com/riscv/riscv-gnu-toolchain
git clone https://github.com/riscv/riscv-pk
git clone https://github.com/riscv/riscv-isa-sim
```

Set environment variables (preferably move these commands into bashrc, zshrc, or something similar)
```
export RISCV=/path/from/home/to/RISCV
export PATH=$PATH:$RISCV/bin
```

Build the toolchain (this will take a while so make some tea or something)
```
cd riscv-gnu-toolchain
mkdir build
cd build
../configure --prefix=$RISCV --with-arch=rv32i
make
```

Now, to build the Proxy Kernel
```
cd ../riscv-pk
mkdir build
cd build
../configure --prefix=$RISCV --host=riscv32-unknown-elf
make
make install
```

Finally to build Spike
```
cd ../riscv-isa-sim
mkdir build
cd build
../configure --prefix=$RISCV --enable-histogram
make
make install
```

You are done!! To test if your install is working correctly use the following:
```
riscv32-unknown-elf-gcc helloworld.c # where helloworld.c is any C program
RISCV/riscv-isa-sim/build/spike --isa=RV32IMAC RISCV/riscv-pk/build/pk a.out
```



