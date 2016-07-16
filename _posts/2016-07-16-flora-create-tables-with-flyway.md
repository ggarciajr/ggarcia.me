---
layout: post
title: "Flora Time Tracker - Creating the database tables with Flyway"
excerpt: "Preparing the backend for the free session page."
date: 2016-07-16
tags: [Haskell, Backend, Flyway]
author: ggarciajr
comments: true
---

**Flyway** is an open-source database migration tool and we will use it to create the initial tables that we will need for the project and also to further evolve the database schema as we move forward.

This post does not explain how to install **Flyway**. Installation instructions and information about how Flyway's command line tool works can be found in the Flyway's official <a href="https://flywaydb.org" rel="noopener noreferrer" target="_blank">website</a>.

<div class="spacer">
  <div class="mask"></div>
</div>

To run the migrations with **Flyway**, execute the following command from the project's root folder. We assume that the flyway binary is in your OS PATH and can be executed from any folder.

```shell
flyway -user=<db-user> \
       -password=<db-password> \
       -url=jdbc:postgresql://localhost:5432/flora \
       -locations=filesystem:migrations \
       migrate
```
