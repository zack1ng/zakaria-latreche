---
title: Energy-Based Swing-Up Inverted Pendulum Using LQR Controller for Stabilization
status: closed
tags: [LQR, Control Systems, Energy Pumping, ROS2, GZ]
image: https://github.com/zack1ng/zakaria-latreche/releases/download/swingup_pendulum/swingup_pendulum_19.mp4
layout: project
description: This project discusses the control of a swing-up inverted pendulum using an energy-based approach and an LQR controller. It covers the implementation in ROS2, starting with ros2_control and the choice of the ros2_control interface. Presents the mathematical model of the system, starting with the dynamics derived using the Lagrangian equation, followed by linearization. A pumping force approach is used to reach the upright equilibrium state, then the system switches to the LQR controller for stabilization.
order: 3
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
