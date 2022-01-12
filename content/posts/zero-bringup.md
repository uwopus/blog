---
title: "Raspberry Pi Zero Bringup"
date: 2022-01-11T20:15:51-05:00
draft: false
---

The Raspberry Pi Zero 2 is the main computer for the robot, and performs the path planning and high-level control for Opus. Although there is a previous post that outlines how to install CAN on a Pi Zero, it was determined that the CAN implementation would take much more time for insignificant returns. This post outlines how the initial setup was done on the Zero so that it is able to build programs for the Pico, program it, and development can start on the communication. 

## Systems Outline
The Zero 2 W is connected to a main board that provides power, an IMU connected over I2C and a port to communicate with the Raspberry Pi Pico board over SPI. This port also contains a SWD interface and power. This enables the Zero to program and debug the Pico. 

## Setup

First, the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) tool was used to program Raspberry Pi Buster Lite onto an SD card. Advanced settings were used (accessible with ctrl+shift+x) to set the hostname to `Opus`, setup the Wi-Fi information and enable SSH on first boot. This allows the Pi to immediately be controlled wirelessly without having to purchase a microUSB to USB adapter to connect a keyboard and mouse.

Once booted, SPI and I2C were enabled with `sudo rpi-config`. The following steps were run: 
```bash
sudo apt update
sudo apt upgrade
sudo apt install git vim tmux gdb-multiarch
```

Next, the dependencies for programming the Pico were installed as per the Getting Started Guide. 

We added this to the `~/.bashrc`: 
```bash
export PICO_SDK_PATH=/home/pi/pico/pico-sdk
```

OpenOCD was also installed with the following instructions from the Getting Started with Pico guide: 

```bash
cd ~/pico
sudo apt install automake autoconf build-essential texinfo libtool libftdi-dev libusb-1.0-0-dev
git clone https://github.com/raspberrypi/openocd.git --branch rp2040 --depth=1 --no-single-branch
cd openocd
./bootstrap
./configure --enable-sysfsgpio --enable-bcm2835gpio
make -j4
sudo make install
```
> Note: The `./bootstrap` step might fail on platforms other than Raspbian because of some certificate errors. If this is the case, fix the certificates or manually download those repositories.

An SSH key for accessing the [Opus Github Account](https://www.github.com/uwopus) was installed. 

VS Code remote access was installed and the C/C++ extension pack was installed along with Cortex Debug. 

Finally, VS Code was tested on building and debugging the code, using these guides: 
- [Programming Pi Pico with SWD](https://www.electronicshub.org/programming-raspberry-pi-pico-with-swd/)
- [How to Debug Your Project](https://blog.smittytone.net/2021/02/05/how-to-debug-a-raspberry-pi-pico-with-a-mac-swd/)
    - This one uses picoprobe, but we're bitbanging so the interface was changed to `/interface/raspberrypi-swd.cfg`
    - The `gdbPath` was changed to `gdb-multiarch`

The `launch.json` file looked like this: 

```json
{
    "version": "0.2.0",
    "configurations": [
      { "name": "Pico Debug",
        "device": "RP2040",
        "gdbPath": "gdb-multiarch",
        "cwd": "${workspaceRoot}",
        "executable": "${command:cmake.launchTargetPath}",
        "request": "launch",
        "type": "cortex-debug",
        "servertype": "openocd",
        "configFiles": [
          "/interface/raspberrypi-swd.cfg",
          "/target/rp2040.cfg"
        ],
        "svdFile": "${env:PICO_SDK_PATH}/src/rp2040/hardware_regs/rp2040.svd",
        "runToMain": true,
        "postRestartCommands": [
          "break main",
          "continue"
        ]
      }
    ]
   }
```


Debugging works, including breakpoints!

## Image
An image of the SD card was taken with `dd`. It was attempted to reduce the partition size with `gparted` before this to reduce the image size, but it did not work. 