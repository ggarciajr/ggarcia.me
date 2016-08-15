---
layout: post
title: "IntelliJ changing the default JRE"
excerpt: "How run IntelliJ using a different JRE."
date: 2016-08-15
tags: [IntelliJ, ArchLinux, JRE]
author: ggarciajr
comments: true
---

IntelliJ on Linux uses a custom JRE, based on OpenJDK to run. To run InlliJ with a different JRE you will need to modify the **idea.jdk** file, which is located in the config folder.

The file content should consist of one line pointing to the home path of the custom JRE. So, for example, if you have the Oracle JRE installed in the **/opt/oracle/java8** folder, then, your **idea.jdk** shoud look like as follows:


```shell
/opt/oracle/java8
```

More information about this can be found in <a href="https://intellij-support.jetbrains.com/hc/en-us/articles/206544879-Selecting-the-JDK-version-the-IDE-will-run-under" rel="noopener noreferrer" target="_blank">here</a>.
