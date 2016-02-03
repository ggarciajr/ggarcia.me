---
layout: post
title: "Flora Time Tracker - Updating dependencies"
excerpt: "Set stack to use lts-4.1 instead of lts-3.20 and updating the project dependencies."
date: 2016-01-14
tags: [Haskell, Stack, Dependencies, GHC]
author: ggarciajr
comments: true
---

To update the dependencies we need to modify the **stack.yaml** to tell stack to use version 4.1 of the <a href="http://www.stackage.org/" target="_blank">Stackage</a> lts.

Stackage is a stable source of Haskell packages that guarantee that haskell packages build consistently, thus avoiding the **cabal hell** problem.

```diff
--- a/stack.yaml
+++ b/stack.yaml
@@ -1,7 +1,7 @@

-resolver: lts-3.20
+resolver: lts-4.1
```

After updating the **stack.yaml** we need to remove the version constraints of our dependencies so we can use the versions specified by the Stackage lts.

```diff
--- a/flora.cabal
+++ b/flora.cabal
@@ -48,40 +48,44 @@ library
                 RecordWildCards

     build-depends: base                          >= 4          && < 5
-                 , yesod                         >= 1.4.1      && < 1.5
-                 , yesod-core                    >= 1.4.6      && < 1.5
-                 , yesod-auth                    >= 1.4.0      && < 1.5
-                 , yesod-static                  >= 1.4.0.3    && < 1.6
-                 , yesod-form                    >= 1.4.0      && < 1.5
-                 , classy-prelude                >= 0.10.2
-                 , classy-prelude-conduit        >= 0.10.2
-                 , classy-prelude-yesod          >= 0.10.2
-                 , bytestring                    >= 0.9        && < 0.11
-                 , text                          >= 0.11       && < 2.0
-                 , persistent                    >= 2.0        && < 2.3
-                 , persistent-postgresql         >= 2.1.1      && < 2.3
-                 , persistent-template           >= 2.0        && < 2.3
+                 , yesod
+                 , yesod-core
+                 , yesod-auth
+                 , yesod-static
+                 , yesod-form
+                 , classy-prelude
+                 , classy-prelude-conduit
+                 , classy-prelude-yesod
+                 , bytestring
+                 , text
+                 , persistent
+                 , persistent-postgresql
+                 , persistent-template
                  , template-haskell
-                 , shakespeare                   >= 2.0        && < 2.1
-                 , hjsmin                        >= 0.1        && < 0.2
-                 , monad-control                 >= 0.3        && < 1.1
-                 , wai-extra                     >= 3.0        && < 3.1
-                 , yaml                          >= 0.8        && < 0.9
-                 , http-conduit                  >= 2.1        && < 2.2
-                 , directory                     >= 1.1        && < 1.3
-                 , warp                          >= 3.0        && < 3.2
+                 , shakespeare
+                 , hjsmin
+                 , monad-control
+                 , wai-extra
+                 , yaml
+                 , http-conduit
+                 , directory
+                 , warp
                  , data-default
-                 , aeson                         >= 0.6        && < 0.10
-                 , conduit                       >= 1.0        && < 2.0
-                 , monad-logger                  >= 0.3        && < 0.4
-                 , fast-logger                   >= 2.2        && < 2.5
-                 , wai-logger                    >= 2.2        && < 2.3
+                 , aeson
+                 , conduit
+                 , monad-logger
+                 , fast-logger
+                 , wai-logger
                  , file-embed
                  , safe
                  , unordered-containers
                  , containers
                  , vector
                  , time
+                 -- yesod Could not find module 'Foreign.Store'
+                 -- need to find a better way so there is no need
+                 -- to include this in the final binary.
+                 , foreign-store

 executable         flora
     if flag(library-only)
@@ -118,7 +122,7 @@ test-suite test

     build-depends: base
                  , flora
-                 , yesod-test >= 1.4.3 && < 1.5
+                 , yesod-test
                  , yesod-core
                  , yesod
                  , persistent
@@ -127,6 +131,6 @@ test-suite test
                  , monad-logger
                  , shakespeare
                  , transformers
-                 , hspec >= 2.0.0
+                 , hspec
                  , classy-prelude
                  , classy-prelude-yesod
```

If you don't want to remove the dependencies constraints, you'll need to update the constraints to match those in the Stackage lts.

We can rebuild the application once both files - **stack.yaml** and **flora cabal** - are updated. In order to do that, run the following commands:

```shell
# setup stack to get the appropriate GHC
stack setup

# install the appropriate versions of yesod-bin and cabal
stack install yesod-bin-1.4.17 cabal-install

# just to be sure, we are going to rebuild yesod-bin
stack build yesod-bin-1.4.17 .
```

Now we can run the application via ghci:

```shell
stack ghci
#only necessary when starting ghci
:l app/DevelMain.hs
# execute everytime you change a file or when you want to reload the application.
:hoge
```
