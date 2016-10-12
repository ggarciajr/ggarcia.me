---
layout: post
title: "Arch Linux - SSD Trim on encrypted LVM volumes"
excerpt: "How to enable SSD trim on encrypted LVM volumes"
date: 2016-10-11
tags: [Installation, Arch Linux, LVM, Encryption, SSD]
author: ggarciajr
comments: true
---

During the <a href="/2016/01/30/installing-arch-linux.html" rel="noopener noreferrer" target="_blank">installation process</a>, we encrypted the entire SSD and then we set the LVM inside the encrypted disk. Then we set the LVM to hold the different partitions **/**, **/home**, and etc.

We now have a SSD that looks like the following image.

<img src="/img/posts/ssd-trim.png"/>

To trim the SSD properly we'll need to enable it on all three layers - **LUKS**, **LVM**, and **filesystem**.

Install the <a hre="https://www.archlinux.org/packages/?name=util-linux" rel="noopener noreferrer" target="_blank">util-linux</a> package and enable the **fstrim.timer** service. This service will activate the **fstrim.service** when necessary.

```shell
sudo pacman -S util-linux
sudo systemctl enable fstrim.timer

# optional step, if you want to run fstrim.service
sudo systemctl start fstrim.service
```

The second step is to configure the LVM layer. Edit the **/etc/lvm/lvm.conf** by changing the **issue_discards** from **0** to **1**.

The last step is to configure the root filesystem by adding the right kernel parameter to the bootloader configuration.

Edit the **/boot/loader/entries/\<your-entry\>.conf** file and append **:allow-discards** at the end of the **cryptdevice** option and append **rd.luks.options=discard** at the end of the **options** line.

Your conf file should look like the following:

```shell
title    Arch Linux Encrypted via UUID
linux    /vmlinuz-linux
initrd   /initramfs-linux.img
options  cryptdevice=UUID=<DEV_UUID>:vgroup00:allow-discards root=UUID=<UUID> quite rw rd.luks.options=discard
```

**Note:** DEV_UUID is the UUID of the device containing the LUKS partition. LVM_VOL_UUID is the UUID of the LVM root volume. You can get those UUIDs using the command **blkid**.

For more information, please refer to the <a href="https://wiki.archlinux.org/index.php/Solid_State_Driveshttps://wiki.archlinux.org/index.php/Solid_State_Drives" rel="noopener noreferrer" target="_blank">Arch Linux wiki</a>
