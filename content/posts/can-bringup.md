---
title: "Bringing up CAN on a Pi Zero 2"
date: 2021-12-17T22:15:51-05:00
draft: false
---

## The Problem 
[Most](https://www.beyondlogic.org/adding-can-controller-area-network-to-the-raspberry-pi/) [guides](https://www.hackster.io/youness/how-to-connect-raspberry-pi-to-can-bus-b60235) online to enable CAN with SocketCAN on the Raspberry Pi, it is assumed that the MCP2515 is used. This chip is _extremely_ popular, but because of the supply chain issues stemming from COVID-19, it is also very hard to find. When we ordered the components for Opus's main board, we were lucky enough to find some [MCP 2510](https://www.microchip.com/en-us/product/MCP2510) chips in stock on Digikey. Since the Linux driver is called `mcp251x`, we assumed that it would be pretty easy to setup!

## Solution (WIP) 
Thought that just using the instructions from the aforementioned blog posts would work, just needed to change the `mcp2515` to `mcp2510`. This was really naive since the overlay just didn't exist. Figured out that you can decompile `.dtbo` files from [this](https://stackoverflow.com/questions/21670967/how-to-compile-dts-linux-device-tree-source-files-to-dtb) post. 

So, we used this process: 
1. Decompile the `/boot/overlays/mcp2515-can0.dtbo` file with `dtc -I dtb -O dts mcp2515-can0.dtbo > mcp2515-can0.dts`
2. Make a copy of the dtc file called `mcp2510-can0.dts`
3. Replace all mentions of `mcp2515` with `mcp2510` in that file. 
4. Recompile this with `dtc -O dtb -o mcp2510-can0.dtbo mcp2510-can0.dts`

Put it in `/boot/overlays` and edit the `/boot/config.txt` file to have the following lines: 

```bash
dtoverlay=mcp2510-can0,oscillator=8000000,interrupt=19
dtoverlay=spi0-hw-cs
```

Success-ish! Now, the logs show up in `dmesg` instead of nothing like before, but it still says `Wrong Wiring?`
