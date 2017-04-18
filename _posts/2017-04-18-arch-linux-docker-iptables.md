---
layout: post
title: "Arch Linux - Docker and iptables"
excerpt: "How to make docker and iptables to get along."
date: 2017-04-18
tags: [Docker, Arch Linux, iptables]
author: ggarciajr
comments: true
---

After enabling iptables I started to have the following errors when running docker containers

```shell
(iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 2701 -j DNAT --to-destination 172.17.0.2:28017 ! -i d
ocker0: iptables: No chain/target/match by that name.\n (exit status 1))
```

To fix the problem, I changed the systemd docker unit file and added the option **--no-iptables**

Here is the solution:

```shell
# stop docker service
systemctl stop docker

# get the ExecStart line from the current unit file
grep ExecStart /etc/systemd/system/docker.service

# edit the unit file
systemctl edit docker
```

The content of the `override.conf` file that will be created should be the `ExecStart` line from the current unit file with `--no-iptables` appended to the end.

```shell
# example
# original: ExecStart=/usr/bin/dockerd -H fd://
# new value: ExecStart=/usr/bin/dockerd -H fd:// --no-iptables
```
