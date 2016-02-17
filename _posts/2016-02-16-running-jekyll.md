---
layout: post
title: "Running Jekyll locally in a docker container"
excerpt: "How to run Jekyll locally in a docker container."
date: 2016-02-16
tags: [Docker, Blog, Jekyll]
author: ggarciajr
comments: true
---

If you want to test locally how your blog or project page will look like when published to <a href="https://pages.github.com/" target="_blank">Github Pages</a>, you can run <a href="http://jekyllrb.com/" target="_blank">Jekyll</a> inside a docker container.

To run the Jekyll docker container you need to run the following command from the root of your Jekyll site.

```shell
docker run --rm --label=jekyll --volume=$(pwd):/srv/jekyll \
  -it -p 127.0.0.1:4000:4000 jekyll/jekyll:stable
```

If you are using <a href="http://direnv.net/" target="_blank">direnv</a>, you can create a executable script, add it to your PATH environment variable and use it to start the containe.

Create a file under the **bin** folder in the root folder of your project and create a file called **jekyll** inside of it. The content of the file is as follows:

```shell
#!/bin/zsh

docker run --rm --label=jekyll --volume=$(pwd):/srv/jekyll \
  -it -p 127.0.0.1:4000:4000 jekyll/jekyll:stable
```

**Note**: Don't forget to make the file executable.

Then edit the **.envrc** file by adding the following entry into it:

```shell
PATH_add bin
```

To run the container, you will just need to run **jekyll** from the root folder of your project.

```shell
jekyll
```
