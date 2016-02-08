---
layout: post
title: "Docker Machine and Docker Compose, installation and configuration"
excerpt: "Setting up a development environment or Arch Linux using Docker, Machine and Compose."
date: 2016-02-03
tags: [Docker, Docker Machine, Docker Compose, Machine, Compose, Development, Environment]
author: ggarciajr
comments: true
---

### Rationale

The idea is to setup isolated development environment that can be used for several projects without the need to start all different services needed by different projects every time the computer is booted.

Lets say we have three projects X, Y, and Z where X and Y uses Postgres while Z uses MySql.

Instead of starting both Postgres and MySql when our computer starts, we want to start Postgres when we are working on project X and Y or, start MySql when we are working on project Z.

### Docker Compose

> Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a Compose file to configure your application’s services.

### Docker Machine

According to the Docker Machine documentation:

> Docker Machine allows you to provision Docker on virtual machines that reside either on your local system or on a cloud provider. Docker Machine creates a host on a VM and you use the Docker Engine client as needed to  build images and create containers on the host.

<div class="spacer">
  <div class="mask"></div>
</div>

### Installing

We'll need to install Docker, Docker Compose, and Docker Machine. Notice that <a href="https://wiki.archlinux.org/index.php/VirtualBox#Installation_steps_for_Arch_Linux_hosts" target="_blank">Virtualbox</a> is needed to run a docker machine locally.

```bash
sudo pacman -S docker virtualbox
yaourt -S docker-compose-bin docker-machine
sudo modprobe vboxdrv
```

Load the virtualbox kernel modules by adding the following line in the **/etc/modules-load.d/virtualbox.conf** file.

~~~
vboxdrv
vboxnetadp
vboxnetflt
vboxpci
~~~

If you want to point the Docker images folder to a different path, you will need to edi the systemd service as follows:

```bash
systemctl edit --full docker
```

```diff
-ExecStart=/usr/bin/docker daemon -H fd:// --exec-opt native.cgroupdriver=cgroupfs
+ExecStart=/usr/bin/docker daemon --graph='/path/to/custom/folder/Docker/' -H fd:// --exec-opt native.cgroupdriver=cgroupfs
```

Enable and start daemon

```bash
systemctl enable docker
systemctl start docker
```

To run docker as your regular user and without the need to **sudo**, you will needto add your user to the **docker** group.

```bash
gpasswd -a <user> docker
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Creating a new machine

```bash
# ip class must be different than your local network ip
# i.e: local network = 192.168.0.1
# docker machine ip should be something like 192.168.50.1/24
docker-machine create --virtualbox-hostonly-cidr <ip> -d virtualbox <machine-name>
```

To tell Docker to talk to that machine you need to run the following command:

```bash
eval "$(docker-machine env <machine-name>)"
```

Starting and Stopping machines

```bash
docker-machine stop <machine-name>
docker-machine start <machine-name>
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Docker Compose

To use Docker Compose you need to create a file called **docker-compose.yml** in the root folder of your project.

Here is an example of such file:

```yml
docker-compose.yml
postgres:
  container_name: my_psql_container
  restart: always
  image: paintedfox/postgresql
  volumes:
    - /my/local/filesystem/some-dir:/data
  ports:
    - "127.0.0.1:5432:5432"
```

This file is telling Docker Compose to run a container using the **paintedfox/postgresql** image and to attach the container's **/data** folder to **/my/local/filesystem/some-dir** folder in our local filesystem. It is also telling the container to export the port **5432**.

**Note**: Each container has its own set of parameters and configurations, this is just an example.

Then you can start the environment described by the **docker-compose.yml** file by running the following command:

```bash
docker-compose up -d
```

And you can stop the environment by running:

```bash
docker-compose stop
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Docker Basic commands

**Running an Arch linux container**

* -t assigns a pseudo-tty or terminal to the container
* -i allow to make an interactive connection by grabbing the standard in (STDIN) of the container.
* -d to run the container as daemon

```bash
docker run -t -i base/archlinux /bin/bash
```

**Checking running containers**

```bash
docker ps
```

**Reading container logs**

To get the container name see 'Checking running containers'

* -f makes the container logs to act like 'tail -f'

```bash
docker logs <container_name>
```

**Start / Stop running containers**

```bash
docker start <container_name>
docker stop <container_name>
```

**Checking docker client commands**

```bash
docker
```

**Checking command help**

```bash
docker <command> --help
```

**Inspecting docker container info**

```bash
docker inspect <container_name>
```

**Removing containers**

```bash
docker rm <container_name>
```

**Listing images**

```bash
docker images
```

**Pulling images**

```bash
docker pull <image_name>
```

**Searching images**

```bash
docker search <term>
```
