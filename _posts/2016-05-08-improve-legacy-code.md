---
layout: post
title: "Improve legacy code"
excerpt: "My quick check list on things I do to improve legacy code."
date: 2016-05-08
tags: [code, test, legacy code, legacy, improve, improvement]
author: ggarciajr
comments: true
---

This is the checklist I use when dealing with legacy code that is neither tested nor well documented.

1. Try to understand the code - read it and make notes.
2. Identify possible candidates for refactor and improvement.
3. If possible, check with the person that wrote the code whenever you have doubts about why something does what it does or why it was written in a particular way.
4. Check with stakeholders or whomever knows about the business about how the part you are refactoring should work.
5. Write unit tests. Integration tests should be written If it is not possible to write unit tests before refactoring.
6. Document the functionality you are refactoring (i.e: code comment, wiki entry, etc).
7. Fix the issues identified on step 2.
8. Jump to step 2.
