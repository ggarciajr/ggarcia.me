---
layout: post
title: "Flora Time Tracker - Using Atom as a Haskell IDE"
excerpt: "How to setup your development environment to use Atom as your IDE."
date: 2016-01-08
tags: [Time, Time Tracker, Haskell, Yesod, Haskell Web Application, Atom, IDE, ghci, stack]
author: ggarciajr
comments: true
---

# ide-haskell

IDE-Haskell is a plugin for the Atom editor and it opens automatically whenever
you open a haskell source file.

Combine it with **language-haskell**, **haskell-ghc-mod**, **autocomplete-haskell** and
you get a great IDE with support for autocompletion and linter your Haskell projects

### Suggested Atom packages:
 * <a href="https://atom.io/packages/language-shakespeare" target="_blank">language-shakespeare</a>
 * <a href="https://atom.io/packages/language-haskell" target="_blank">language-haskell</a>
 * <a href="https://atom.io/packages/ide-haskell" target="_blank">ide-haskell</a>
 * <a href="https://atom.io/packages/haskell-ghc-mod" target="_blank">haskell-ghc-mod</a>
 * <a href="https://atom.io/packages/autocomplete-haskell" target="_blank">autocomplete-haskell</a>

The installation process has two steps. The first one is to install **ghc-mod**, **hlint** and **stylish-haskell**. The second one is the installation of atom packages.

You can modify the apm command to include or remove packages as you wish.

{% highlight bash %}
stack install ghc-mod hlint stylish-haskell
apm install language-haskell haskell-ghc-mod ide-haskell autocomplete-haskell
{% endhighlight %}

## Issues with *yesod devel*

**ghc-mod** will fail if it sees a **dist/** directory which is made when you run your yesod projectt via stack exec yesod devel.

The workaround, until yesod-bin is updated to use Stack directly, is to delete the **/dist** directory and instead of yesod devel, run the application via ghci.

## GHCI and auto-reload

To reload an application inside a **ghci** session we need to enter three commands:

{% highlight bash %}
# when opening the session to load everyithing needed.
:l app/DevelMain.hs

# to reload the application
DevelMain.shutdown
:l app/DevelMain.hs
DevelMain.update
{% endhighlight %}

We use **:l app/DevelMain.hs** instead of **:reload** because the former doesn't
reload shakespeare templates.

Entering those commands manually every time we want to reload the application can be
very tedious but we can make our lives easier.

Modify the .ghci file by including these lines onto the end of the file:

{% highlight bash %}
:l app/DevelMain.hs
:def hoge const $ return $ unlines ["DevelMain.shutdown",":l app/DevelMain.hs","DevelMain.update"]
{% endhighlight %}

Then run the application via ghci:

{% highlight bash %}
stack ghci
#only necessary when starting ghci
:l app/DevelMain.hs
# execute everytime you change a file or when you want to reload the application.
:hoge
{% endhighlight %}
