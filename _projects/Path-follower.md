---
title: Path finder & follower
status: open
tags: [ROS2, PID, Control systems, Computer Vision]
layout: project
---

this tutorial is for explaining how to build a differential drive robot capable of recognizing a line and follow it also avoid objects. the steering of the robot is handled using a PID, and the objects are detected using VFH Algorithm.
## Implementation:
this project is a real implementation of a differential robot capable of navigating based on a specific motif, in our case a black line placed on the ground formed on multiple shapes and forms. we will simulate a closed loop system the block diagram on Figure 1 shows the overall fllow of our system.
<figure class="alpha">
  <img src="{{ '/assets/projects/path_follower/path_follow_block.png' | relative_url }}" alt="path_follow_block">
  <figcaption><strong>Figure 1</strong>. Path follower pipeline</figcaption>
</figure>


our robot is equipped with a two sensors a LiDAR used by the local planner and a camera, this camera is the main brain of our system once a line is inside our frame we process to perform image processing using OpenCV where we get the frame, our whole matrix is transferred into grayscale then we need to read y how much the centre of the white line is away from the centre of the bottom of our frame. by calculating the difference we indirectly calculated by how much our robot is far from the line. the calculated error is then transferred as a message type of float64.
after calculating the error we proceed to finding our steering command and to realize that we relay on a PID controller where we feed the error into the PID and we select our own P, I and D parameters the output of our PID controller is then feed into diff drive controller as an angular velocity.
the steering nodes and our local planner node are not related to each other and handeled using BT's (behviour tree), where we relay on leaf nodes to get a feedback from both nodes and a decision is made about which node is needed. 
