---
layout: post
title: "Installing Arch linux"
excerpt: "How to install arch linux with UEFI, LVM, and an encrpyted HD."
date: 2016-01-30
tags: [Installation, Arch Linux, LVM, Encryption]
author: ggarciajr
comments: true
---

At the end of this post we should have a running Arch linux box with the following characteristcs.

* UEFI enabled boot.
* Encrypted LVM volume.

<div class="spacer">
  <div class="mask"></div>
</div>

### Table of contents

1. <a href="#installation_media">Istallation media</a>
2. <a href="#blank_screen">Blank Screen</a>
3. <a href="#keyboard_layout">Set the keyboard layout</a>
4. <a href="#uefi_mode">UEFI mode</a>
5. <a href="#connect_internet">Connect to the internet</a>
6. <a href="#update_system_clock">Update the system clock</a>
7. <a href="#prepare_storage">Prepare the storage devices</a>
8. <a href="#encrypting">Encrypting</a>
9. <a href="#setting_lvm">Setting up the LVM</a>
10. <a href="#installing_arch">Installing Arch</a>
11. <a href="#configure_network">Configure the network</a>
12. <a href="#user_pwd">Users and passwords</a>
13. <a href="#reboot">Reboot</a>

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="installation_media"></a>

### Istallation media

Specific information about how to generate the installation from different platforms can be found in the <a href="https://wiki.archlinux.org/index.php/USB_flash_installation_media" target="_blank">arch official wiki</a>

**Usb stick on Linux**

Find out the name of the usb.

```shell
lsblk
```

Use the **dd** comand to copy the arch linux iso image to your usb stick. Replace **/dev/sdx** by your usb drive - i.e: **/dev/sdc** - without the partition number.

```shell
dd bs=4M if=<path-to-iso-file> of=/dev/sdx status=progress && sync
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="blank_screen"></a>

### Blank screen

If you get a blank screen after booting you will need to pass **nomodeset** to the kernel.

**Syslinux**

Press **tab** when the menu shows up and add the **nomodeset** ad the end of the string. The following strings are just to ilustrate the end result.

```shell
linux /boot/vmlinuz-linux root=/dev/sda3 initrd=/boot/initramfs-linux.img nomodeset
```

**systemd-boot**

Press **e** when the menu shows up and add the **nomodeset** ad the end of the string

```shell
initrd=\initramfs-linux.img root=/dev/sda2 nomodeset
```

**grub**

Press **e** when the menu shows up and add the **nomodeset** ad the end of the string

```shell
linux /boot/vmlinuz-linux root=UUID=978e3e81-8048-4ae1-8a06-aa727458e8ff nomodeset
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="keyboard_layout"></a>

### Set the keyboard layout

You can change the keyboard layout if don't use the default layout - qwerty.

```shell
# list of available layouts
localectl list-keymaps

# if you use dvorak
loadkeys dvorak
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="uefi_mode"></a>

### UEFI mode

To verify you are booted in UEFI mode, check that the following directory is populated.

```shell
ls /sys/firmware/efi/efivars
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="connect_internet"></a>

### Connect to the internet

Find you wifi interface.

```shell
ip link
```

Configure the wifi connection using the wifi-menu.

```shell
wifi-menu -o <name-of-wifi-interface>
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="update_system_clock"></a>

### Update the system clock

Use <a href="https://wiki.archlinux.org/index.php/Systemd-timesyncd" target="_blank">systemd-timesyncd</a> to ensure that your system clock is accurate.

```shell
timedatectl set-ntp true
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="prepare_storage"></a>

### Prepare the storage devices

Wipe your entire disk. This can take a long time depending on the size of the disk.

Use the **lsblk** command to find the name of the disc before using this command.

```shell
dd if=/dev/zero of=/dev/sdx iflag=nocache oflag=direct bs=4096
```

Make sure **dm-mod** and **dm_crypt** modules are loaded.

```shell
modprobe -a dm-mod dm_crypt
```

Run parted to open the device whose partition table must be created.

```shell
parted /dev/sdx
```

Create the required EFI System Partition.

```shell
# 512MiB is suggested by arch documentation.
mkpart ESP fat32 1MiB 513MiB
set 1 boot on
```

Create the LVM partition.

```shell
mkpart primary ext4 513MiB 100%
set 2 lvm on
```

Quit parted.

```shell
quit
```

Format the EFI System Partition.

```shell
# use lsblk /dev/<disc-name> to find the partition number.
mkfs.fat -F32 /dev/sdx1
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="encrypting"></a>

### Encrypting

We will use **cryptsetup** to encrpyt our lvm partition.

```shell
# -v = verbose
# -y = verify password, ask twice, and complain if they don’t match
# -c = specify the cipher used
# -s = specify the key size used
# -h = specify the hash used
# -i = number of milliseconds to spend passphrase processing
# –use-random = which random number generator to use
# luksFormat = to initialize the partition and set a passphrase
# /dev/sda2 = the partition to encrypt (this is just an ilustration.)
cryptsetup -v -y -c aes-xts-plain64 -s 512 -h sha512 -i 5000 --use-random luksFormat /dev/sda2
```

Save the header information of the LUKS device.

```shell
cryptsetup luksDump /dev/sda2 > /tmp/luksDump.txt
```

Open the LUKS device so we can setup the LVM on it.

```shell
# mounts the device at /dev/mapper/crypto
cryptsetup luksOpen /dev/sda2 crypto
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="setting_lvm"></a>

### Setting up the LVM

Create a physical volume on /dev/mapper/crypto

```shell
pvcreate /dev/mapper/crypto
```

Create a volume group called **vgroup00** but you can name it whatever you want.

```shell
vgcreate vgroup00 /dev/mapper/crypto
```

Create the logical volumes.

* **lvolswap** will be our swap partition.
* **lvolroot** will be our **/** partition.
* **lvolhome** will be our **/home** partition.

```shell
lvcreate -C y -L 8GB vgroup00 -n lvolswap
lvcreate -L 100GB vgroup00 -n lvolroot
lvcreate -l +100%FREE vgroup00 -n lvolhome
```

Scan the volume groups and import any changes.

```shell
vgscan
vgchange -ay
```

Create the filesystem on each logical volume.

```shell
mkswap /dev/mapper/vgroup00-lvolswap
mkfs.ext4 /dev/mapper/vgroup00-lvolroot
mkfs.ext4 /dev/mapper/vgroup00-lvolhome
```

Prepare the newly created filesystems to receive Arch.

```shell
swapon /dev/mapper/vgroup00-lvolswap
mount /dev/mapper/vgroup00-lvolroot /mnt
mkdir -p /mnt/boot
mount /dev/sda1 /mnt/boot
mkdir /mnt/home
mount /dev/mapper/vgroup00-lvolhome /mnt/home
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="installing_arch"></a>

### Installing Arch

Refresh the package list.

```shell
pacman -Syy
```

Use pacstrap to install the base-system. You will have to press **enter** twice to confirm the installation of all packages. One when asked to confirm the packages from the base group and another to when asked about the packages for the base-devel group.

```shell
pacstrap -i /mnt base base-devel
```

Generate the **fstab** file.

```shell
genfstab -U /mnt > /mnt/etc/fstab
```

**Change root.**

```shell
arch-chroot /mnt /bin/bash
```

**Set the locale.**

Uncomment your preferred encoding from the **/etc/locale.gen** file, generate the locales, and create the **/etc/locale.conf** file.

```shell
vi /etc/locale.gen
locale-gen
# replace en_US.UTF-8 by the encoding you uncommented in /etc/locale.gen file
echo LANG=en_US.UTF-8 > /etc/locale.conf
export LANG=en_US.UTF-8
```

**Set the time.**

```shell
tzselect
# Zeno/Subzone is the value from tzselect
ln -s /usr/share/zoneinfo/Zone/SubZone /etc/localtime
hwclock --systohc --utc
```

**Regenerate Initramfs.**

Edit the **etc/mkinitcpio.conf** as follows:

```shell
vi /etc/mkinitcpio.conf
```

~~~
# before
HOOKS="base udev autodetect modconf block filesystems keyboard fsck"

# after
HOOKS="base udev autodetect modconf block keyboard encrypt lvm2 filesystems fsck"
~~~

The hooks keymap, encrypt, lvm2, and resume need to come between block and filesystems. The shutdown hook is after the filesystems entry.

```shell
mkinitcpio -p linux
```

**Install the bootloader.**

```shell
# pacman -S intel-ucode is only necessary if you have an Intel CPU.
pacman -S intel-ucode
bootctl install
```

Edit the **/boot/loader/loader.conf**

~~~
default  arch-encrypted
timeout  4
editor   0
~~~

**Note:** the default value is the name of the entry you want to use as default. In our case it will be **arch-encrypted-nomodeset** or **arch-encrypted**.


Create a file called **/boot/loader/entries/arch-encrypted-nomodeset.conf** with the following content to add a boot entry.

~~~
title          Arch Linux (Encrypted - Nomodeset)
linux          /vmlinuz-linux
initrd         /initramfs-linux.img
options        cryptdevice=UUID=<DEV_UUID>:vgroup00 root=UUID=<LVM_VOL_UUID> quiet rw nomodeset
~~~

**Note:** **nomodeset** is only necessary if you have the blank screen issue.

**Note:** If you have an Intel video card and you want to use the **xf86-video-intell** driver you must not use the **nomodeset**. You can set GDM and create the **arch-encrypted.conf** as follows:

**Note:** DEV_UUID is the UUID of the device containing the LUKS partition. LVM_VOL_UUID is the UUID of the LVM root volume. You can get those UUIDs using the command **blkid**.

~~~
title          Arch Linux (Encrypted)
linux          /vmlinuz-linux
initrd         /initramfs-linux.img
options        cryptdevice=/dev/sda2:vgroup00 root=UUID=<UUID> quiet rw
~~~

**Note:** By creating the two files if something goes wrong with one setting you can just reboot and choose the other one in the boot loader.

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="configure_network"></a>

### Configure the network

**Set the hostname.**

```shell
# replace Arch by a name of your preference.
echo Arch > /ect/hostname
```

Change the **/etc/hosts** file.

```shell
# replace#
# /etc/hosts: static lookup table for host names
#

#\<ip-address>  \<hostname.domain.org>  \<hostname>
127.0.0.1       localhost.localdomain   localhost  Arch
::1             localhost.localdomain   localhost  Arch

# End of file Arch by a name of your preference.
echo Arch > /ect/hostname
```

Install the necessary packages to configure the wireless connection.

```shell
pacman -S iw wpa_supplicant dialog
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="user_pwd"></a>

### Users and passwords

**Set the root password.**

```shell
passwd
```

**Create a user for you.**

```shell
# replace username by your username.
useradd -m -g users -s /bin/bash username
passwd username
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="reboot"></a>

### Reboot

Run the following commands and then remove the USD stick from the computer.

```shell
exit
umount -R /mnt
reboot
```
