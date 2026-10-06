--- 
layout: post
title: "Foxglove TFs not Rendering Issue"
---

This is a fix to the issue related to robot_model topic is not being well interpreted by foxglove bridge, where some tfs can not be rendered due to how mesh files are exported locally, all the packages that are exported from fusion360 to urdf can possibly face the same issue.
all the exported meshes are referred to as:
```xml
<mesh filename="file://$(find rosbot_mk3_description)/meshes/base_link.stl" scale="0.001 0.001 0.001"/>
```
this will expose the meshes files locally in our container and to bypass this issue, all we need to the change from file navigation to packages as follow:
```xml
<mesh filename="package://rosbot_mk3_description/meshes/base_link.stl" scale="0.001 0.001 0.001"/>
```
this way any file required by gz or ros2, can not be seen, and in order to make it visible all we need to do is to export the path to our pkg:
```bash
export GZ_SIM_RESOURCE_PATH=$GZ_SIM_RESOURCE_PATH:/hum_ws/install/rosbot_mk3_description/share
```


