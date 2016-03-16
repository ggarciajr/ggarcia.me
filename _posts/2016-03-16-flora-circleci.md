---
layout: post
title: "Flora Time Tracker - Adding Circle CI"
excerpt: "Running backend and frontend tests upon every new commit pushed to github using Circle CI."
date: 2016-03-16
tags: [Haskell, JavaScript, Circle CI, Stack, Test]
author: ggarciajr
comments: true
---

Lets add support to <a href="https://circleci.com/" target="_blank">Circle CI</a> to make sure the code is tested regularly. The idea is to have Circle CI to build and run the tests so we can have confidence that the project is in a health state.

The first thing you will need is to create an account on the <a href="https://circleci.com/" target="_blank">Circle CI</a> website and add the project so Circle CI can start to monitor your repository and start the building process as soon as new commits arrive.

Having the account created and the project added, we need to create the configuration file - *circle.yml* - in the project's root folder. This file will tell Circle CI about the project dependencies and what commands to run.

```yml
machine:
  # exports environment variables
  environment:
    PGUSER: postgres
    PGDATABASE: circle_test
    PGPORT: 5432
    PGHOST: localhost

dependencies:
  cache_directories:
    # Frontend app javascript modules
    - "js/node_modules"
    # Backend app haskell modules
    - "~/.stack"

  pre:
    # Install stack
    - wget -q -O- https://s3.amazonaws.com/download.fpcomplete.com/ubuntu/fpco.key | sudo apt-key add -
    - echo 'deb http://download.fpcomplete.com/ubuntu/precise stable main' | sudo tee /etc/apt/sources.list.d/fpco.list
    - sudo apt-get update && sudo apt-get install stack -y

  override:
    # Build javascript dependencies
    - cd js && npm install --save-dev
    # Build haskell dependencies
    - stack setup && stack build --test --only-dependencies

test:
  override:
    # run js tests
    - cd js && npm test
    # run haskell tests
    - stack test
```

<div class="spacer">
  <div class="mask"></div>
</div>

### Configuring Yesod

We will need to change the *test-settings.yml* and the *TestImport.hs* files so Yesod will handle settings with environment variables when running the tests.

First we tell Yesod that *flora_test* is the default test database and that it can be overwritten by the *PGDATABASE* environment variable.

```diff
--- a/config/test-settings.yml
+++ b/config/test-settings.yml
@@ -1,2 +1,2 @@
 database:
-  database: flora_test
+  database: "_env:PGDATABASE:flora_test"
```

Secondly, we need to change the *TestImport.hs* file because it ignores environment variables by default.

```diff
diff --git a/test/TestImport.hs b/test/TestImport.hs
index 625acce..ad76ec4 100644
--- a/test/TestImport.hs
+++ b/test/TestImport.hs
@@ -11,7 +11,7 @@ import Foundation            as X
 import Model                 as X
 import Test.Hspec            as X
 import Text.Shakespeare.Text (st)
-import Yesod.Default.Config2 (ignoreEnv, loadAppSettings)
+import Yesod.Default.Config2 (useEnv, loadAppSettings)
 import Yesod.Test            as X

 runDB :: SqlPersistM a -> YesodExample App a
@@ -28,7 +28,7 @@ withApp = before $ do
     settings <- loadAppSettings
         ["config/test-settings.yml", "config/settings.yml"]
         []
-        ignoreEnv
+        useEnv
     foundation <- makeFoundation settings
     wipeDB foundation
     logWare <- liftIO $ makeLogWare foundation
```

<div class="spacer">
  <div class="mask"></div>
</div>

Now, every time you push a commit to github, Circle CI will build the project and run the tests.

### Bonus

You can access the *Project settings* -> *Status badges* menu in the Circle CI platform and generate a badge for you to put in your project README file.
The badge will change as the last build build successfully or not.

This is how it looks like:

<img src="/img/posts/circle-ci-badge.png"/>
