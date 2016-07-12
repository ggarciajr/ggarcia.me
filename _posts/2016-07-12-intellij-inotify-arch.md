---
layout: post
title: "IntelliJ inotify issue on ArchLinux"
excerpt: "How to set inotify value to make IntelliJ happy."
date: 2016-07-12
tags: [IntelliJ, ArchLinux, inotify]
author: ggarciajr
comments: true
---

To fix the warning about **fs.inotify.max_user_watches** the IntelliJ shows, it is necessary to set a value for the **fs.inotify.max_user_watches** and then apply the change.

Here are the commands necessary to fix the issue on ArchLinux:

```shell
sudo echo 'fs.inotify.max_user_watches = 524288' >>/usr/lib/sysctl.d/50-default.conf
sudo sysctl -p --system
```

More information about this can be found in <a href="https://confluence.jetbrains.com/display/IDEADEV/Inotify+Watches+Limit" rel="noopener noreferrer" target="_blank">here</a> and in <a href="https://bbs.archlinux.org/viewtopic.php?id=193020"  rel="noopener noreferrer" target="_blank">here</a>.
