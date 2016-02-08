---
layout: post
title: "Arch linux - Configuring Gnome and installing some base tools."
excerpt: "Configuring Gnome"
date: 2016-02-01
tags: [Installation, Arch Linux, Gnome, Workstation, Desktop]
author: ggarciajr
comments: true
---

### Sudo

Add your user to the sudoers list

Modify the **/etc/sudoers** file with the following command and add yourself just below the root entry.

```bash
visudo -f /etc/sudoers

# find this line root ALL=(ALL) ALL
# and add yourself as follows.
<your-user> ALL=(ALL) ALL
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Install yaourt

Install yaourt to make it easier to install packages from the AUR repository.

Download the <a href="https://aur.archlinux.org/packages/package-query/" target="_blank">package-query</a> and <a href="https://aur.archlinux.org/packages/yaourt/" target="_blank">yaourt</a> snapshots from the AUR.


```bash
cd <where-you-saved-the-snapshots>
tar -xzf package-query.tar.gz
tar -xzf yaourt.tar.gz
cd package-query
makepkg -i -s
cd ../yaourt
makepkg -i -s
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Theme

Install the murrine engine and the vertex theme.

```bash
pacman -S gtk-engine-murrine faenza-icon-theggarcia.meme
yaourt -S vertex-themes-git
```

Download the <a href="http://gnome-look.org/content/show.php/Polar+Cursor+Theme?content=27913" target="_blank">Polar<a/> cursor theme.

```bash
mkdir ~/.icons
mv <where-you-saved-the-file> ~/.icons
cd ~/.icons
bzip2 -d 27913-PolarCursorThemes.tar.bz2
tar -xf 27913-PolarCursorThemes.tar
rm 27913-PolarCursorThemes.tar
```

Open the **gnome-tweak-tool**. Go to the **Extensions** option end enable the **User themes** extension.
Close the **gnome-tweak-tool** and open it again. In the **Appearance** tab select:

* **GTK+** -> Vertex
* **Icons** -> Faenza
* **Cursor** -> PolarCursonTheme
* **Shell theme** -> Vertex

In the **Workspaces** tab, set the number of workspaces to 3, or whatever you want, and make the workspace creation static.

You can change the fonts in the fonts tab.

* **Window Titles** -> Avenir Next LT Pro Bold Condensed (size 11)
* **Interface** -> Avenir Next LT Pro Regular (size 11)
* **Documents** -> Avenir Next LT Pro Regular (size 11)
* **Monospace** -> DejaVu Sans Mono Book (size 11)
* **Hinting** -> None
* **Antialiasing** -> Grayscale
* **Scaling Factor** -> 1.00

<div class="spacer">
  <div class="mask"></div>
</div>

### Useful extensions

* Topicon
* Places status indicator
* Pomodoro

Places status indicator comes with gnome. Topicon can be installed via the gnome <a href="https://extensions.gnome.org/extension/495/topicons/" target="_blank">Extensions</a> site.

You can install the Pomodoro extensions using yaourt.

```bash
yaourt -S gnome-shell-pomodoro
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Music

Gstreamer plugins to play mp3, ogg, etc.

```bash
pacman -S --needed --noconfirm gst-plugins-bad gst-plugins-base gstreamer \
                               gstreamer0.10 gstreamer0.10-bad gstreamer0.10-bad-plugins \
                               gstreamer0.10-base-plugins gstreamer0.10-ffmpeg \
                               gstreamer0.10-good gstreamer0.10-good-plugins \
                               gstreamer0.10-python gstreamer0.10-ugly gstreamer0.10-ugly-plugins
```
