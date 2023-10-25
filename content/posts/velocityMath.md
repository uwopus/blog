---
title: "Velocity Math"
date: 2022-03-04T22:15:51-05:00
draft: false
math: true
---


This math allows the robot to translate velocity requirements in the global coordinate frame of x and y to velocities of the ropes. We first defined two coordinate systems and gave names to the various names and angles in the diagram: 

![diagram](/blog/images/math/diagram.png)

Here, $X^S$ and $Y^S$ make up the canvas coordinate frame. This is intentionally chosen to be the same orientation as the [coordinate system in images](https://www.mathworks.com/help/images/image-coordinate-systems.html). $Y^S$ is chosen so its parallel to the gravity vector. $X^R$ and $Y^R$ define the coordinate system that moves with the robot, located at the robot's centre of gravity. 

There are a few values that are constant and known beforehand: 
- $r_1$: The distance between the pegs that Opus is hanging off of
- $r_3$: The distance between the apertures where the rope comes out of on Opus
- $r_L$, $r_R$: The vector between the centre of gravity and the apertures defined in the $X^RY^R$ frame
- $F_g$: The force of gravity

$\theta$ is the angle of the robot's frame relative to the paper frame, 

$\theta$ is typically not known, but we designed Opus with an IMU. With a Attitude Heading Reference System Kalman Filter (AHRS), we are able to get stable $\theta$ values from the device, so we treat this as a known value. 

A transformation matrix is used to change bases from the x, y coordinate frame to the robot wire coordinates. 
