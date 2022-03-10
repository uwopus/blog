---
title: "Raspberry Pi Pico Bringup"
date: 2022-01-13T20:15:51-05:00
draft: false
---

## Purpose
The Raspberry Pi Pico is responsible for reading the encoder values from the motors while providing PWM values to the Talon SRXs. The board has two JST XH connectors to connect to the two encoders, and two three pin headers for PWM output. The board also has some status LEDs, a SPI communication port, a SWD interface for debugging and five general purpose input/outputs for future use.

The following photo is the 3D view from KiCAD of the board. 

![Pico3D](/blog/images/pico/pico3D.jpg)

## Soldering
The various components of this board were soldered to facilitate testing. This photo shows the complete product: 

![PicoSoldered](/blog/images/pico/soldered.jpg)

## Toolchain
Once the board was soldered, the toolchain to work with the Pico was installed. For the most part, setting stuff up for the Pico was a breeze. We just followed the instructions in the [Getting Started Guide \[33MB PDF\]](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf) and it worked out of the box for Linux. For Windows, however, some special steps needed to be taken: 

1. The path length maximum needed to be increased, following directions from [Stack Overflow](https://stackoverflow.com/questions/1880321/why-does-the-260-character-path-length-limit-exist-in-windows). 
2. CMake had to be started via the Visual Studio console. 

Other than that, the instructions in the Getting Started guide held. 


