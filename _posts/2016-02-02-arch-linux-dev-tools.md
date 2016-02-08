---
layout: post
title: "Arch linux - Installing and configuring useful tools."
excerpt: "Configuring Gnome"
date: 2016-02-02
tags: [Installation, Arch Linux, Gnome, Workstation, Desktop, zsh, tmux, filezilla, atom]
author: ggarciajr
comments: true
---

### Table of contents

1. <a href="#office">Office Suite, Internet & Email</a>
2. <a href="#password_manager">Password manager</a>
3. <a href="#archive_manager">Archive Manager</a>
4. <a href="#development_utilities">Development utilities</a>
5. <a href="#multimedia">Multimedia & Ebooks</a>
6. <a href="#text_editor">Text Editor</a>
7. <a href="#zsh_tmux">Zsh & Tmux</a>
8. <a href="#photo_design">Photo & Design</a>
9. <a href="#communication">Communication</a>
10. <a href="#miscelaneous">Miscelaneous</a>
11. <a href="#printer">Printer</a>
12. <a href="#java">Java</a>
13. <a href="#intellij">IntelliJ</a>
14. <a href="#sbt">SBT</a>
15. <a href="#clojure">Clojure</a>
16. <a href="#vmware">VMware</a>
17. <a href="#env_variables">Environment variables</a>

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="office"></a>

### Office Suite, Internet & Email

Lets install a few browsers - firefox, chromium, and opera. The Libreoffice office suite. A PDF reader - evince. An email client - thunderbird. A financial manager - homebank.

```bash
pacman -S firefox chromium opera flashplugin thunderbird libreoffice homebank evince
yaourt -S chromium-pepper-flash
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="password_manager"></a>

### Password manager

Figaro password manager is a nice password manager and I use it because it does not store my passwords on some server in the cloud.

```bash
yaourt fpm2
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="archive_manager"></a>

### Archive Manager

Archive manager is an utility software that you can use in the GUI to create compressed files - zip, tar.gz, etc - and to extract files from compressed ones.

```bash
pacman -S file-roller unrar unzip
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="development_utilities"></a>

### Development utilities

FTP, Git, Git-Cola, Meld.

```bash
pacman -S filezilla git meld
yaourt -S git-cola
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="multimedia"></a>

### Multimedia & Ebooks

Asunder, Brasero, Sound Converter, Calibre, Audacious, Gmusicbrowser, Easy tag.

```bash
sudo pacman -S asunder soundconverter brasero calibre audacious easytag
yaourt -S gmusicbrowser
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="text_editor"></a>

### Text Editor

Atom and Vim.

```bash
yaourt -S atom-editor
pacman -S vim
```

Atom packages.

~~~
atom-beautify
autocomplete-haskell
color-picker
column-select
emmet
git-blame
git-plus
haskell-ghc-mod
language-haskell
language-markdown
language-scala
language-shakespeare
minimap
multi-cursor
project-manager
react
react-snippets
wombat-dark-syntax
~~~

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="zsh_tmux"></a>

### Zsh & Tmux

We are going to make zsh our default shell.

```bash
pacman -S zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# change your default shell to zsh
chsh -s /bin/zsh
```

And we will install <a href="https://tmux.github.io/" target="_blank">tmux</a> and <a href="https://github.com/tmuxinator/tmuxinator" target="_blank">tmuxinator</a>.

```bash
pacman -S tmux
yaourt -S tmuxinator
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="photo_design"></a>

### Photo & Design

Gimp, InkScape and DarkTable.

```bash
pacman -S inkscape gimp darktable
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="communication"></a>

### Communication

Weechat, Skype and Pidgin.

```bash
pacman -S weechat skype pidgin
yaourt -S google-talkplugin zoom
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="miscelaneous"></a>

### Miscelaneous

```bash
sudo pacman -S transmission-cli transmission-gtk alsa-utils alsa-plugins gnome-alsamixer openssh parcellite
yaourt -S dropbox
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="printer"></a>

### Printer

Install the printer driver, **cups**, **ghostscript**, **gsfonts**, and enable **cupsd.service**.

```bash
# driver installation for epson l355
yaourt -S epson-inkjet-printer-201207w

pacman -S cups ghostscript gsfonts
systemctl enable org.cups.cupsd.service
```

Access <a href="http://localhost:631/admin" target="_blank">CUPS Admin</a> to add your printer.

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="java"></a>

### Java

Download the Java SE Development Kit from <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Oracle</a>.

Extract the contents of the .tar.gz file.

```bash
tar -xzf jdk-8u71-linux-x64.tar.gz
```

Export the **HOME** folder and add the **bin** folder to your **PATH** variable by adding the following in the **.profile** file.

```bash
JAVA_HOME=<path-to-where-you-extract-the-tar-gz>/java
PATH=$JAVA_HOME/bin:/usr/local/sbin:/usr/local/bin:/usr/bin
export JAVA_HOME
export PATH
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="intellij"></a>

### IntelliJ

Download the IntelliJ from <a href="http://www.jetbrains.com/idea/" target="_blank">Jetbrains</a>

Extract the contents of the .tar.gz file.

```bash
tar -xzf ideaIU-15.0.3.tar.gz
```

Create a file called **intellij.desktop ** in the **.local/share/applications/** with the following content:

~~~
[Desktop Entry]
Name=IntelliJ IDEA Ultimate Edition
Comment=Java, Groovy, Scala and Android applications development
Exec=<path-to-where-you-extract-the-tar-gz>/bin/idea.sh
Path=<path-to-where-you-extract-the-tar-gz>/bin
Terminal=false
Icon=<path-to-where-you-extract-the-tar-gz>bin/idea.png
Type=Application
Categories=Development;IDE
~~~

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="sbt"></a>

### SBT

Download sbt from the <a href="http://www.scala-sbt.org/download.html" target="_blank">scala sbt</a> site.

Extract the contents of the .tgz file.

```bash
tar -xzf sbt-0.13.9.tgz
```

Export the **HOME** folder and add the **bin** folder to your **PATH** variable by adding the following in the **.profile** file.

```bash
SBT_HOME=<path-to-where-you-extract-the-tar-gz>/sbt
PATH=$JAVA_HOME/bin:$SBT_HOME/bin:/usr/local/sbin:/usr/local/bin:/usr/bin
export JAVA_HOME
export SBT_HOME
export PATH
```

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="clojure"></a>

### Clojure

We are going to install leiningen to be able to work with Clojure projects.

```bash
yaourt -S  leiningen
```

<div class="spacer">
  <div class="mask"></div>
</div>

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="vmware"></a>

### VMware

Install the kernel headers.

```bash
sudo pacman -S linux-headers
```

Download the <a href="http://www.vmware.com/products/workstation/workstation-evaluation" target="_blank">VMware workstation</a>.

Execute the installer.

```bash
sudo sh VMware-Workstation-Full-12.1.0-3272444.x86_64.bundle
```

Enable and start the service.

```bash
yaourt -S vmware-systemd-services
systemctl enable vmware
systemctl start vmware
```

**Note**: For the System service scripts directory, use **/etc/init.d**

<div class="spacer">
  <div class="mask"></div>
</div>

<a id="env_variables"></a>

### Environment variables

For managing environment variables for different projects, we will use <a href="http://direnv.net/" target="_blank">direnv</a>.

```bash
yaourt -S  direnv

# if you dont want to keep go installed
pacman -Rsc go
```

Hook the direnv into the zsh by adding the following line at the end of the **~/.zshrc** file:

~~~
eval "$(direnv hook zsh)"
~~~

Direnv does not support aliases. If you want to define an alias you need to create a new folder and add an executable script to run the command you want.

Then you add the folder to your path by adding the following line to your **.envrc** file.

~~~
PATH_add <name-of-the-folder-containing-the-scripts>
~~~

Add your projects environment variables in a file called **.envrc** in the project's root folder.
