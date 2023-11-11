---
title: "Conclusion & Symposium!"
date: 2022-03-18T15:15:51-05:00
draft: false
math: true
---

We presented Opus at the capstone symposium! It was a great success. 

Here's a photo of the finished robot in the middle of a marking session: 

![diagram](/blog/images/integrated_device.jpg)

Here's a video of it drawing a smile!

![smile-webp](/blog/images/smile.webp)

We passed the program a CSV file with a series of waypoints, and whether the marker is engaged for the waypoint or not. The on-board Raspberry Pi Zero makes the trajectory, converts the trajectory to spool speeds, and sends the requested spool speeds to the Pico Board. This board converts the requested speeds into PWM commands to the motor controllers and sends the encoder data back to the Zero, facilitating closed loop control.

We had some challenges with oscillations when we suddenly changed directions. This can be solved with a better system model, more gradual transitions between velocities and not actuating the marker until the oscillation has dampened down. There is also a z-plane bounce when the marker is engaged, which can be resolved with more careful control of the marker actuation mechanism. 

![shaky-webp](/blog/images/shaky.webp)

Overall, this capstone was a wonderful learning experience and it was really rewarding to put all of the things we have learned over the years together into a culminating project!