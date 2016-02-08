---
layout: post
title: "Arch linux - Post installation"
excerpt: "Configuring Arch with Gnome, GDM and the necessary packages to configure the wifi network."
date: 2016-01-31
tags: [Installation, Arch Linux, Gnome, Workstation, Desktop]
author: ggarciajr
comments: true
---

### Configuring the wifi connection

After rebooting your newly installed Arch you need to check if the internet connection is working and configure it again if it is not.

You will need to find the name of the wifi interface:

```shell
ip link
```

And then configure the wifi connection using the **wifi-menu**:

```shell
wifi-menu -o <name-of-wifi-interface>
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Getting the latest updates

Now we will refresh the packages list and update the entire system.

**Note**: You should enable **multilib** repository if you are running 64bit Arch. Uncomment the following two lines from the **/etc/pacman.conf** file.

~~~
[multilib]
Include = /etc/pacman.d/mirrorlist
~~~

Refresh the packages list:

```shell
pacman -Syy
```

Update the entire system:

```shell
pacman -Syu
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Installing the GUI

To install the GUI environment we will need to install the packages from the **xorg** group.

```shell
pacman -S xorg xorg-server xorg-xinit xorg-server-utils
```

And the packages from the **gnome** group.

```shell
pacman -S gnome gedit gnome-tweak-tool
```

**Note**:Press **enter** to confirm the installation of all packages in the **gnome** group.

**Note**: Choose **1** if you are asked about **libx264** and **libx264-10bit** and about **xf86-input-evdev** and **xf86-input-libinput**.

At last, enable **gdm** to start at boot time.

```shell
systemctl enable gdm
```

Install the network manager:

```shell
pacman -S networkmanager network-manager-applet networkmanager-openvpn
```

**Note**: networkmanager-openvpn is necessary only if you need to connect to a VPN using OpenVpn

And enable the NetworkManager service.

```shell
systemctl enable NetworkManager
```

Reboot and you should have a graphical login and gnome up and running.

<div class="spacer">
  <div class="mask"></div>
</div>

### NVidia driver

To use the proprietary NVidia driver, you will need to install the following packages:

* bumblebee
* mesa
* xf86-video-intel
* nvidia
* lib32-nvidia-utils
* bbswitch
* nvidia-utils

```shell
sudo pacman -S bumblebee mesa xf86-video-intel nvidia lib32-nvidia-utils bbswitch nvidia-utils
```

Then add your user to the bumblebee group

```shell
sudo gpasswd -a $USER bumblebee
```

And enable the **bumblebeed** service.

```shell
sudo systemctl enable bumblebeed.service
```

You can find more info about bumblebee and the NVidia driver in <a href="https://antergos.com/wiki/hardware/graphics/bumblebee-for-nvidia-optimus/" target="_blank">here</a>.
