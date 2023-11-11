---
title: "Velocity Math"
date: 2022-03-04T22:15:51-05:00
draft: false
math: true
---


This math allows the robot to translate velocity requirements in the global coordinate frame of x and y to velocities of the ropes. We first defined two coordinate systems and gave names to the various names and angles in the diagram: 

![diagram](/blog/images/math/updated_diagram.png)

Here, $X^S$ and $Y^S$ make up the canvas coordinate frame. This is intentionally chosen to be the same orientation as the [coordinate system in images](https://www.mathworks.com/help/images/image-coordinate-systems.html). $Y^S$ is chosen so its parallel to the gravity vector. $X^R$ and $Y^R$ define the coordinate system that moves with the robot, located at the robot's centre of gravity. 

There are a few values that are constant and known beforehand: 
- $r_1$: The distance between the pegs that Opus is hanging off of
- $r_3$: The distance between the apertures where the rope comes out of on Opus
- $r_L$, $r_R$: The vector between the centre of gravity and the apertures defined in the $X^RY^R$ frame
- $F_g$: The force of gravity

$\alpha$ is the angle of the robot's frame relative to the wall's frame. $\alpha$ is typically not known, but we designed Opus with an IMU. This allowed us to use an Attitude Heading Reference System Kalman Filter (AHRS) to get stable $\alpha$ values from the device. We use the output of the AHRS Kalman Filter as a _known_ $\alpha$ value. 

For a given location in the $X^S$, $Y^S$ frame, we need to find the lengths $r_2$ and $r_4$ that result in it. 

This looks suspiciously like a four bar linkage, with the added constraint that beams $r_2$ and $r_4$ _cannot_ be in compression, since they are cables. Therefore, the four-bar linkage governing formula from _Fundamentals of Machine Theory and Mechanisms_ was used as a starting point, and we came to the following formulae: 

**Givens:** 

Due to the $y$ position being the same on the anchors: $\theta_1 = 0$

**Formula:**

$$
\begin{align}
x_3 &= r_1\cos{\theta_1} - r_3\cos{\alpha} \\\\
y_3 &= r_1\sin{\theta_1} - r_3\sin{\alpha}
\end{align}
$$


A transformation matrix is used to change bases from the x, y coordinate frame to the robot wire coordinates. 
