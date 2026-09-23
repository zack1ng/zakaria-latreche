---
title: Inverted Pendulum Control
status: open
tags: [ros2_control, Gazebo, PID]
layout: project
---

## Overview

A prismatic cart carries a rotary arm. The goal is to swing/hold the arm
at 90° by driving cart velocity — the classic inverted-pendulum balancing
problem, done on a custom ROS2/Gazebo model instead of a textbook
Simulink block.

## Approach

- Modeled the cart-pendulum system in Gazebo with two joints wired
  through `ros2_control`.
- Drove the cart with a `forward_command_controller` publishing velocity
  commands.
- Closed the loop with a PID controller reading pendulum angle error and
  correcting cart velocity in real time.

## What I learned

Tuning PID gains against a system with this little damping margin, and
getting a feel for how ros2_control's controller interfaces map onto a
real actuation loop.
