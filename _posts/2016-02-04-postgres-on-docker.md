---
layout: post
title: "Docker - Running a PostgreSql container"
excerpt: "Setting up a PostgreSql container with Docker Compose"
date: 2016-02-04
tags: [Docker, Docker Compose, Compose, Development, Environment, PostgreSql]
author: ggarciajr
comments: true
---

This file is telling Docker Compose to run a container using the **paintedfox/postgresql** image and to attach the container's **/data** folder to **/my/local/filesystem/some-dir** folder in our local filesystem. It is also telling the container to export the port **5432**.

```yml
docker-compose.yml
postgres:
  container_name: my_psql_container
  restart: always
  image: paintedfox/postgresql
  volumes:
    - /my/local/filesystem/some-dir:/data
  ports:
    - "127.0.0.1:5432:5432"

```

**Note**: don't use the **-d** option the first time you start the container so you can get the username and password created during the postgresql db initialization.

Starting the container:

```shell
docker-compose up -d
```

Stoping the container:

```shell
docker-compose stop
```
