---
layout: post
title: "Integration of Robotics Applications using ROS2, Docker and GitHub"
---
This is a guide through the ROS 2 Dockerization. Development of robotics applications requires a reproducible environment, and that's where Docker comes in. Using Docker Compose, we can have an Ubuntu image ready to use with our desired ROS2 version, including the all necessary files and packages.
First, we need to install WSL (in our case we will use ROS2 Humble, so we install Ubuntu 22.04). WSL can be installed using the MS Store by searching for Ubuntu + version, or using the command line. Inside PowerShell run `wsl --install -d Ubuntu-22.04`. Once the installation is completed, run `wsl -l -v`. This command lists the available WSL machines and their status (stopped or running). Use `wsl -d machine_name` to enter one.
Once inside your WSL machine, install Docker using the following commands. First, delete all the conflicting packages:
```bash
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc docker-buildx podman-docker containerd runc | cut -f1)
```
Then run:
```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
After the installation is completed, check the Docker status using `sudo systemctl status docker`.This command outputs "running" if Docker is running. If the status is "stopped", start the service using `sudo systemctl start docker`.
Next, we need to give our containers access to the GPU. For that we install the NVIDIA Container Toolkit inside WSL:
```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
  sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt update
sudo apt install -y nvidia-container-toolkit
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```
To check that Docker can see the GPU, run:
```bash
docker run --rm --gpus all ubuntu nvidia-smi
```
If you see a table with your GPU information (GPU model and update version), everything is working.
Before we set up our Docker image file, we need to set up our directory. The directory needs to respect the following structure:
```text
main_directory/
├── docker-compose.yml
├── Dockerfile
└── ros2_ws/
```
After WSL is set up along with Docker, we move to the most important part: the Dockerfile. This file is mainly responsible for creating the Docker image, including the necessary ROS2 packages.
Here is how the Dockerfile is structured:
```dockerfile
FROM osrf/ros:humble-desktop-full

ENV DEBIAN_FRONTEND=noninteractive

RUN echo "source /opt/ros/humble/setup.bash" >> /root/.bashrc
RUN echo '[ -f /ros2_ws/install/setup.bash ] && source /ros2_ws/install/setup.bash' >> /root/.bashrc

RUN apt-get update && apt-get install -y \
    ros-${ROS_DISTRO}-foxglove-bridge \
    ros-${ROS_DISTRO}-ros-gz \
    ros-${ROS_DISTRO}-ros2-control \
    ros-${ROS_DISTRO}-ros2-controllers \
    ros-${ROS_DISTRO}-gz-ros2-control \
    ros-${ROS_DISTRO}-robot-localization \
    ros-${ROS_DISTRO}-navigation2 \
    ros-${ROS_DISTRO}-nav2-bringup \
    ros-${ROS_DISTRO}-slam-toolbox \
    ros-${ROS_DISTRO}-turtlebot3-gazebo \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR /ros2_ws

CMD ["bash"]
```

First, we import the ROS Humble image; here we choose desktop-full. Then we make every new terminal source ROS2 automatically. For our workspace, we first check that it has been built (`[ -f ... ]`), because before the first `colcon build` the file `install/setup.bash` doesn't exist yet and we would get an error every time we open a terminal. After that we install the necessary packages and remove the installation files, and finally we set our work directory.
Once the Dockerfile is set up, we move to the `docker-compose.yml`. This file is responsible for configuring how our containers run and for setting up services:
```yaml
services:
  ros2_dev:
    build: .
    container_name: ros2_ws
    environment:
      - DISPLAY=${DISPLAY}
      - WAYLAND_DISPLAY=${WAYLAND_DISPLAY}
      - XDG_RUNTIME_DIR=${XDG_RUNTIME_DIR}
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix
      - /mnt/wslg:/mnt/wslg
      - ./ros2_ws:/ros2_ws
    ports:
      - "8765:8765" # <- foxglove port is exposed to the main host
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
    stdin_open: true #  container interactive
    tty: true
```

In my case, this configuration file runs a ROS2 container named `ros2_ws`, under a service called `ros2_dev`.
Once the service is built, we move to setting up the environment. This part is very important, since running ROS2 inside a container can be difficult when it comes to visualization: the container has no screen of its own, so RViz2 and Gazebo can't open their windows. That's why we set up our application to display graphical windows using the `environment` variables and the shared display folders, which connect the container to WSLg. The `deploy` section gives the container access to our GPU (this is why we installed the NVIDIA Container Toolkit earlier).
Then we set up volumes. This is also important, since we want to share files between the main system and the container. Any change we make to our code on the main system appears inside the container, and our files stay safe even if the container is deleted.
Currently I am using **Foxglove** as a tool for debugging and visualization. That's why we set up port 8765, which maps the container's network port to the main system so Foxglove can connect to the bridge running inside the container.
After setting up our files, we move to building our container by running:
```bash
docker compose up -d --build
```

We don't need to run the build command every time. After the container is built, all we need to do to start it is:
```bash
docker compose up -d
```

We only need to build again when we change the Dockerfile (for example, when we add a new ROS2 package), because the image has to be rebuilt to include the change.
Once the build has ended, we run `docker ps` to list the currently running containers. To enter the container, we run:
```bash
docker exec -it ros2_ws bash
```
We use `docker compose stop` to stop the container. This command is important, since sometimes the interface bugs, so we need to stop our container and start it again:
```bash
docker compose stop
docker compose up -d
```
If we want to stop the container and remove it, we run:
```bash
docker compose down
```
Removing the container doesn't delete our code, since it lives in the `ros2_ws` folder on the main system.
### Version control with GitHub
To maintain our updates in the code, we rely on GitHub repositories to save our changes and make them available to contributors.
There are multiple ways of pushing our code, but most of them need a login or a temporary access token. I personally prefer to use SSH, since it doesn't need a login or a temporary token.
The first thing to do is to generate an SSH key. In the terminal (inside WSL) run:

```bash
ssh-keygen
cat ~/.ssh/id_ed25519.pub
```

The first command generates a public and a private SSH key. It will ask for a file location and a passphrase; pressing Enter accepts the defaults. We only need the public key, which is stored in the `.pub` file inside the `.ssh` directory, and the second command prints it. Never share the private key.
The public key needs to be copied and pasted in GitHub: Settings -> SSH and GPG keys -> New SSH key. Give a name to the key and paste it in the space reserved for the key. To check that it works, run:

```bash
ssh -T git@github.com
```

Next, we create a GitHub repository and copy the SSH link of the repo. We run `git init` inside `main_directory`, so the repository includes the `Dockerfile` and the `docker-compose.yml` along with our workspace. This way, anyone can clone it and run the whole environment.

```bash
git init
git remote add origin git@github.com:username/repo_name.git
```
Before adding our files, we create a `.gitignore` file so we don't push the generated folders:
```text
build/
install/
log/
```
then we push our workspace
```bash
git add .
git commit -m "first commit"
git branch -M main
git push -u origin main
```

This is my experience with using Docker Compose with ROS 2 for robotics development. Sometimes, running ROS 2 on VirtualBox can't be convenient since most of the time VMs are not reliable and require a lot of resources. Docker containers offer the best alternative for someone who can't give up Windows. Docker images offer the best performance when it comes to simulation. Based on my experience, having a machine with a Linux-based operating system is a must since they are more convenient, but still, using Docker Compose for robotics development is more reliable since it ensures that the project is working for all the contributors no matter which operating system we are using.
