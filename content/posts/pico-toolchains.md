---
title: "Setting up Raspberry Pi Pico Toolchains"
date: 2022-01-08T22:15:51-05:00
draft: false
---

We're using the [Raspberry Pi Pico](https://www.raspberrypi.com/documentation/microcontrollers/raspberry-pi-pico.html) on a carrier board to handle PWM generation and motor encoder click counting. This post outlines how we setup the toolchains on various devices.

![Pico Board Soldered](/blog/images/pico/pico-board.jpg 'Soldered!')

For the most part, setting stuff up for the Pico was a breeze. We just followed the instructions in the [Getting Started Guide \[33MB PDF\]](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf) and it worked out of the box for Linux. For Windows, however, some special steps needed to be taken: 

1. The path length maximum needed to be increased, following directions from [Stack Overflow](https://stackoverflow.com/questions/1880321/why-does-the-260-character-path-length-limit-exist-in-windows). 
2. CMake had to be started via the Visual Studio console. 

Other than that, the instructions in the Getting Started guide held. 