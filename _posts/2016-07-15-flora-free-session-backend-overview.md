---
layout: post
title: "Flora Time Tracker - Free session backend overview"
excerpt: "Small and quick overview about the free session page."
date: 2016-07-15
tags: [Haskell, Backend]
author: ggarciajr
comments: true
---

The free session allows users to use the time tracker without the need to register. The idea is that the application will redirect the user to a page using a unique url, whenever the user clicks the 'Try for free' button in the home page.

Every activity, the user creates inside the free session page, can be accessed later by visiting the unique url that was generated the first time the user accessed the free session page.

These are the database tables that we will need to make this happen:

 * free_sessions.
 * projects.
 * activities.
 * users.

Here is how the backend will work when the user first access the free session page:

 * Generates an unique token.
 * Creates an entry in the user table.
 * Creates an entry in the projects table.
 * Creates an entry in the free session table
 * Link the free session record with an user and with a project.
 * Redirects the user to /free/<unique-token>.

The backend will work as follows when the user access the free session using the unique URL:

 * Find the free session record in the database.
 * Find the associated user record.
 * Find the associated project record.
 * Find the associated activity records.
 * Load everything in the browser.
 * The user will get a 404 error if there is no free session associated with the unique URL.
