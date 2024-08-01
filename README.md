# General Build and Install Instructions
## Install
Install make, gcc, and kernel headers for your distribution. Then run:
```sh
make
sudo make install
sudo modprobe ctn91xx
```

## Usage
You should see a network interface when you run `ifconfig -a` called `ctn0`

You can set a static IP address on the 192.168.200.0/24 subnet (don't
use 192.168.200.1) or just use a dhcp client to get an IP address for it.

The InfiniTV device webpage will be available at http://192.168.200.1
From there you can tune via frequency or channel number. Only ClearQAM and
CCI=0 content is available on linux due to lack of DRM support.

Access video via a special device file created under:
`/dev/ceton/ctn91xx_mpeg0_0-5`

You can run `mplayer -cache 8192 /dev/ceton/ctn91xx_mpeg0_0` to play video
off the first tuner. Depending on your system, extra buffering via the shell 
might improve performance. E.g.
```sh
cat /dev/ceton/ctn91xx_mpeg0_0 | mplayer -cache 8192 -
```

## Multiple Cards
More than one InfiniTV is handled by creating more network interfaces
(ctn1,ctn2,etc...). The IP assignment scheme is:
* 192.168.200.1
* 192.168.201.2
* 192.168.202.3
* 192.168.203.4
* etc..

# Using This Repo With DKMS
[DKMS](https://github.com/dell/dkms) is a handy way of making sure that kernel modules are rebuilt and installed when kernels and other components of a system are updated or installed.

## Installation
Make sure `dkms` is installed on your system (`apt install dkms` on Ubuntu), and run the following in your terminal:
```sh
## Go to the kernel module source directory
cd /usr/src/

## Clone this repo and name it in a way that DKMS can understand
sudo git clone https://github.com/JamesRHarris/infinitv_pcie.git ctn91xx-1.0

## Add the module to dkms
sudo dkms add ctn91xx/1.0

## Build the modules for installed kernels
sudo dkms autoinstall ctn91xx-1.0
```

Now you should just have to reboot and the module should be loaded, and will automatically be rebuilt for future kernel updates!

## Updating DKMS When This Repo Changes
```sh
## Go to the kernel module source directory
cd /usr/src/

## Pull the latest changes
sudo git pull

## Build the modules for installed kernels
sudo dkms autoinstall ctn91xx-1.0
```
