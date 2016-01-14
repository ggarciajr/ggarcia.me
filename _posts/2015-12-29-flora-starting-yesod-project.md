---
layout: post
title: "Flora Time Tracker - Starting an Yesod Project"
excerpt: "In this post we'll see how to create the skeleton for a new yesod project."
date: 2015-12-29
tags: [Time, Time Tracker, Haskell, Yesod, Haskell Web Application]
author: ggarciajr
comments: true
---

# Stack

Stack has a good <a href="http://docs.haskellstack.org/en/stable/install_and_upgrade.html" target="_blank">documentation</a> about the installation process on different platforms that you can refer to learn to install it on your computer.

# Creating the project

We first need to install yesod-bin and cabal:

{% highlight bash %}
stack install yesod-bin-1.4.16.1 cabal-install
{% endhighlight %}

Then, we initialize the project:

{% highlight bash %}
stack exec yesod init
{% endhighlight %}

Yesod will ask a few questions about the project. Here are the answers:

{% highlight bash %}
Project name: flora
Database option: p
{% endhighlight %}

And create the **stack.yaml** file:

{% highlight bash %}
cd flora
stack init
{% endhighlight %}

At last we need to get the project dependencies and, just to be safe,
rebuild yesod-bin with the current GHC:

{% highlight bash %}
stack build yesod-bin-1.4.16.1 .
{% endhighlight %}

And now we are good to run the project:

{% highlight bash %}
stack exec yesod devel
{% endhighlight %}

Go to <a href="http://localhost:3000" target="_blank">http://localhost:3000</a>
and you should see the following page:

<img src="/img/posts/yesod-default-template.png"/>
