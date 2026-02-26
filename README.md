# Lab 1: Docker Basics and Cgroups Limits

## 1) Download and Install Docker
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

## 2) Pull nginx:alpine Image
```bash
docker pull nginx:alpine
```
![Docker pull Output](image.png)

## 3) Run nginx:alpine in the Background
```bash
docker run -d --name Tarek-ITI-46 nginx:alpine
```
<img width="585" height="85" alt="image" src="https://github.com/user-attachments/assets/bb55583d-c37b-45bf-a2f6-78f6f51263fa" />


## 4) Run Apache Container with Resource Limits
```bash
docker run -d --name apache-limit --memory="70m" --cpus="1.0" httpd
```
<img width="720" height="271" alt="image" src="https://github.com/user-attachments/assets/2ea8446d-0389-4643-9ea0-74cd8a208947" />

Command Breakdown:

-d: Runs the container in Detached mode (background).

--name apache-limit: Assigns a custom name to the container for easier management.

--memory="70m": Sets a hard limit on the RAM usage using Cgroups.

--cpus="1.0": Restricts the container to use a maximum of 1 CPU core

Cgroups Effect (Resource Control):

When this command runs, the Linux Kernel creates a directory in the host's control group hierarchy: /sys/fs/cgroup/system.slice/docker-02c1eee187a9ea421d5d1c5f83622f4a63572cba5770c11464085f259f7fb3ba.scope

Inside this directory, the file memory.max is updated with the value 73400320 (which is exactly 70MB in bytes)

The Kernel monitors the container's processes; if they attempt to exceed 70MB, the Kernel will intervene (Throttling or OOM Kill).

<img width="896" height="241" alt="image" src="https://github.com/user-attachments/assets/68532c13-bdae-40fe-b857-8d2d290590ca" />





