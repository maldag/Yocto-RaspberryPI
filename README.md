# Yocto-RaspberryPI

##Pre-requisites

To build RaspberryPi from this repository it is necessary to install google repo tool to fetch the Yocto layers recipes.
The repo tool uses a manifest found in this repository (default.xml) to fetch the Yocto layers specifically needed for this build.

Instructions for installing repo can be found here (you only need to care about the Installing Repo section):
http://source.android.com/source/downloading.html#installing-repo

I'm using a docker image based off of this repo:
	https://github.com/gmacario/easy-build
There is a build-yocto Dockerfile that is useful.

##1. Fetch all git trees
Initialize your local working repository:
```
$ mkdir -p ~/projects/rpi
$ cd ~/projects/rpi
$ repo init -u https://github.com/gt3389b/Yocto-RaspberryPI.git -b master
```
Checkout all project trees:
```
$ repo sync
```
##2. Run the build setup script (this will create a build folder)

```
$ source poky/oe-init-build-env buildrpi
```
##3. Edit buildrpi/conf/bblayers.conf and buildrpi/local.conf

For build/conf/bblayers.conf
```
BBLAYERS ?= " \
  /home/anathoo/projects/genivi-rpi2/poky/meta \
  /home/anathoo/projects/genivi-rpi2/poky/meta-yocto \
  /home/anathoo/projects/genivi-rpi2/poky/meta-yocto-bsp \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-openembedded \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-raspberrypi \
  "
BBLAYERS_NON_REMOVABLE ?= " \
  /home/anathoo/projects/genivi-rpi2/poky/meta \
  /home/anathoo/projects/genivi-rpi2/poky/meta-yocto \
  "
```
For build/conf/local.conf
```
MACHINE ??= "raspberrypi3"
GPU_MEM = "128"
CORE_IMAGE_EXTRA_INSTALL += "wayland weston"
LICENSE_FLAGS_WHITELIST  += "commercial"
PREFERRED_VERSION_weston ?="1.6.0"
MULTI_PROVIDER_WHITELIST += " \
             virtual/libgl \
             virtual/egl \
             virtual/libgles2 \
             virtual/mesa \
             "
```

```
#Comment out to avoid bitbake error with some GPLv3 licensed components             
#INCOMPATIBLE_LICENSE ?= "GPLv3"
```
##4. Start the build

```
$ bitbake -v core-minimal
```
##5. Flash image on the SD card


Replace sdX with the correct device ID
```
$sudo umount /dev/sdX
$sudo dd if=./tmp/deploy/images/raspberrypi2/raspberrypi3.rpi-sdimg  of=/dev/sdX bs=128M
$sync
```
