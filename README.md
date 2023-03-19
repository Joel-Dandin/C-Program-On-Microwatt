# Run C Programs on Microwatt

Microwatt is a VHDL2008-based POWER ISA 3.0 core, originally written by Anton Blanchard, supporting Linux, MicroPython, and Zephyr RTOS.

It is also supported in FuseSoC for quick simulation and FPGA bringup!


# Steps

Create a new directory
```
mkdir /home/$USER/uwatt
cd /home/$USER/uwatt
```
Download PowerPC cross-toolchain

---
**NOTE**

You can select powerpc64le-power8 and glibc on toolchains.bootlin.com or get the stable version as of date directly with

---


```
wget https://toolchains.bootlin.com/downloads/releases/toolchains/powerpc64le-power8/tarballs/powerpc64le-power8--glibc--stable-2020.02-2.tar.bz2

tar -xvf powerpc64le-power8--glibc--stable-2020.02-2.tar.bz2

export PATH=$PATH:/home/$USER/uwatt/powerpc64le-power8--glibc--stable-2020.02-2/bin

export CROSS_COMPILE=powerpc64le-linux-
```

Build Micropython
```
cd micropython/ports/powerpc
sudo apt install make
make -j$(nproc)
cd ../../../
```

Build GHDL from source
```
sudo apt install llvm clang gnat zlib1g-dev
```

Install gnat4.9 packages (required by GHDL):
```
wget http://archive.ubuntu.com/ubuntu/pool/universe/g/gnat-4.9/gnat-4.9-base_4.9.3-3ubuntu5_amd64.deb
wget http://archive.ubuntu.com/ubuntu/pool/universe/g/gnat-4.9/libgnat-4.9_4.9.3-3ubuntu5_amd64.deb
sudo dpkg -i ./gnat-4.9-base_4.9.3-3ubuntu5_amd64.deb
sudo dpkg -i ./libgnat-4.9_4.9.3-3ubuntu5_amd64.deb
```

Clone GHDL from GitHub
---
**NOTE**

v0.37 does not support llvm10, in which case you will need to specify the older version of llvm above. The master branch doesn't have this issue.

---


```
cd ghdl
mkdir -p /home/$USER/uwatt/ghdl_build
mkdir build && cd build
../configure --with-llvm-config --prefix=/home/$USER/uwatt/ghdl_build
make
make install
cd ../../
```

After building, add ghdl to the path with
```
export PATH=$PATH:/home/$USER/uwatt/ghdl_build/bin
```

Build Microwatt!
```
cd microwatt
make
```

Link Micropython image
```
ln -s micropython/firmware.bin main_ram.bin
```

Run Microwatt

It sends all output logs to /dev/null
```
./core_tb > /dev/null
```

Running bare-metal C code on Microwatt
```
cd ~/uwatt/microwatt/hello_world 
mv -f Makefile1 Makefile
make
cd ..
./core_tb > /dev/null
```

Run joel_test.c which prints sum of square's of 1 to 10 using c.
```
cd hello_world
make clean
make FILE=joel_test
cd ..
./core_tb > /dev/null
```

To test other functionalities of softcore run other c programs in hello_world folder
```
cd hello_world
make clean
make FILE=sneha
cd ..
./core_tb > /dev/null
```


## Compile Latest Versions

Create a new directory
```
mkdir /home/$USER/uwatt
cd /home/$USER/uwatt
```
Download PowerPC cross-toolchain

```
wget https://toolchains.bootlin.com/downloads/releases/toolchains/powerpc64le-power8/tarballs/powerpc64le-power8--glibc--stable-2022.08-1.tar.bz2

tar -xvf powerpc64le-power8--glibc--stable-2022.08-1.tar.bz2

export PATH=$PATH:/home/$USER/uwatt/powerpc64le-power8--glibc--stable-2022.08-1/bin

export CROSS_COMPILE=powerpc64le-linux-
```

Build Micropython
```
git clone https://github.com/micropython/micropython.git
cd micropython/ports/powerpc
sudo apt install make
make -j$(nproc)
cd ../../../
```

Build GHDL from source
```
sudo apt install llvm clang gnat zlib1g-dev
```

Install gnat4.9 packages (needed by GHDL):
```
wget http://archive.ubuntu.com/ubuntu/pool/universe/g/gnat-4.9/gnat-4.9-base_4.9.3-3ubuntu5_amd64.deb
wget http://archive.ubuntu.com/ubuntu/pool/universe/g/gnat-4.9/libgnat-4.9_4.9.3-3ubuntu5_amd64.deb
sudo dpkg -i ./gnat-4.9-base_4.9.3-3ubuntu5_amd64.deb
sudo dpkg -i ./libgnat-4.9_4.9.3-3ubuntu5_amd64.deb
```

Clone GHDL from GitHub
```
git clone https://github.com/ghdl/ghdl.git
cd ghdl
mkdir -p /home/$USER/uwatt/ghdl_build
mkdir build && cd build
../configure --with-llvm-config --prefix=/home/$USER/uwatt/ghdl_build
make
make install
cd ../../
```

After building, add ghdl to the path with
```
export PATH=$PATH:/home/$USER/uwatt/ghdl_build/bin
```

Build Microwatt!
```
git clone https://github.com/Joel-Dandin/microwatt.git
cd microwatt
make
```

Link Micropython image
```
ln -s micropython/firmware.bin main_ram.bin
```

Run Microwatt!
```
./core_tb > /dev/null
```

# Run code on FPGA
Create Hex file to run on FPGA
```
cd uwatt/microwatt
ln -s micropython/firmware.bin main_ram.bin
cd hello_world
make clean
make FILE=joel_test
```

To flash the hex file to the FPGA use fusesoc
```
fusesoc run --target=nexys_a7 microwatt --ram_init_file=/home/aip/dwatt/microwatt/micropython/firmware.hex
```

# Video
<a href="https://asciinema.org/a/364414" target="_blank"><img src="https://asciinema.org/a/364414.svg" /></a>


