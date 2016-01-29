---
layout: post
title: "Flora Time Tracker - The free route"
excerpt: "Creates the route to the free session page and refactor the home page."
date: 2016-01-29
tags: [Haskell, Stack, Test, React, Webpack, Karma, Npm]
author: ggarciajr
comments: true
---

To create the the free page and make it accessible from the home page we'll need to

* Create a new handler.
* Link the home page and the free page.

# Creating the new route

The creation of a new route involves the following steps:

* create the handler file.
* add the new route in the **config/routes**.
* import the module in the **Application.hs** file.
* add the new module in the **exposed-modules** sections in the **floral.cabal** file.
* create the page hamlet template file - **templates/free.hamlet**.

{% highlight diff %}
--- /dev/null
+++ b/Handler/Free.hs
@@ -0,0 +1,13 @@
+module Handler.Free (
+  getFreeR
+) where
+
+import Import
+
+getFreeR :: Handler Html
+getFreeR = do
+    --master <- getYesod
+
+    defaultLayout $ do
+        setTitleI MsgTitle
+        $(widgetFile "free")

{% endhighlight %}

{% highlight diff %}
--- a/config/routes
+++ b/config/routes
@@ -5,3 +5,5 @@
 /robots.txt RobotsR GET

 / HomeR GET
+
+/free                 FreeR GET
{% endhighlight %}

{% highlight diff %}
--- a/Application.hs
+++ b/Application.hs
@@ -34,6 +34,7 @@ import System.Log.FastLogger                (defaultBufSize, newStdoutLoggerSet,
 -- Don't forget to add new modules to your cabal file!
 import Handler.Common
 import Handler.Home
+import Handler.Free

 -- This line actually creates our YesodDispatch instance. It is the second half
 -- of the call to mkYesodData which occurs in Foundation.hs. Please see the
{% endhighlight %}

{% highlight diff %}
--- a/flora.cabal
+++ b/flora.cabal
@@ -22,6 +22,7 @@ library
                      Settings.StaticFiles
                      Handler.Common
                      Handler.Home
+                     Handler.Free

     if flag(dev) || flag(library-only)
         cpp-options:   -DDEVELOPMENT
{% endhighlight %}

{% highlight diff %}
--- /dev/null
+++ b/templates/free.hamlet
@@ -0,0 +1 @@
+free
{% endhighlight %}

# Linking the home and free session pages

To get the benefits of the type-safe URLs inside the React components, we will
use the same approach as we used to make the navigation component internationlized.

Such approach consists in rendering the URLs as **data-*** attributes of the
tag that is the container of the React component and pass them to the component as
properties.

To render the URL to the free route in hamlet templates we use the following syntax:

{% highlight diff %}
-- Free is the name of the route. R is a suffix used by yesod.
@{FreeR}
{% endhighlight %}

{% highlight diff %}
--- a/templates/homepage.hamlet
+++ b/templates/homepage.hamlet
@@ -1,9 +1,11 @@
 <div class="header" id="header"
-     data-title=_{MsgTitle}
-     data-home=_{MsgNavigationHome}
-     data-try-free="_{MsgNavigationTryFree}"
-     data-sign-up="_{MsgNavigationSignUp}"
-     data-sign-in="_{MsgNavigationSignIn}">
+     data-lang-title=_{MsgTitle}
+     data-lang-home=_{MsgNavigationHome}
+     data-lang-try-free="_{MsgNavigationTryFree}"
+     data-lang-sign-up="_{MsgNavigationSignUp}"
+     data-lang-sign-in="_{MsgNavigationSignIn}"
+     data-link-home="@{HomeR}"
+     data-link-try-free="@{FreeR}">
   <!--- navigation will be rendered here -->

 <div class="splash-container">
@@ -52,7 +54,7 @@
       <p>
         _{MsgRibbonContent}
       <p>
-        <a href="#" class="pure-button primary">_{MsgRibbonTryForFree}
+        <a href=@{FreeR} class="pure-button primary">_{MsgRibbonTryForFree}
{% endhighlight %}

This change will force us to refactor how we create the Navigation component
in the **index.js**

Since we added a prefix in the **data-*** attributes to organize them into
attributes for language and attributes for links, we need to get only the
appropriate ones for each property that the Navigation component requires - i.e:
language and links.

{% highlight diff %}
--- a/js/index.js
+++ b/js/index.js
@@ -1,14 +1,19 @@
 import React from 'react';
 import ReactDOM from 'react-dom';
 import Navigation from './components/navigation.js';
+import DataSetUtils from './utils/dataset.js';

 (function() {
   'use strict';
   // makes Webpack to compile the scss
   require('../scss/main.scss');
+
   const navigationContainer = document.getElementById('header');
+  const language = DataSetUtils.filterAndReduceDataset(navigationContainer.dataset, "lang");
+  const links = DataSetUtils.filterAndReduceDataset(navigationContainer.dataset, "link");
+
   ReactDOM.render(
-    <Navigation language={navigationContainer.dataset}/>,
+    <Navigation language={language} links={links}/>,
     navigationContainer
   );
 })();
{% endhighlight %}

Note the use of the **DataSetUtils**. It is a class that has three functions to
help us filter the **data-*** attributes and get only those we are interested in.

Here is the definition of the **DataSetUtils** class.

{% highlight javascript %}
/* Class to transform data-* attributes from HTML tags     */
/* and transform them into an appropriate format to be fed */
/* React components.                                       */
class DataSetUtils {
  /* dataset attributes become camel case strings - i.e:  */
  /* data-lang-foo becomes langFoo. Since we dont' care   */
  /* about the prefix because we create fully structured  */
  /* json objects, this function is used to remove        */
  /* the prefix and change the first letter of the key    */
  /* to lower case.                                       */
  static normalizeKey(prefix, key) {
    let normalized = '';

    if (key && key.trim().length > 0) {
      const origKey = key.replace(prefix, '');
      normalized = origKey[0].toLowerCase() + origKey.slice(1);
    }

    return normalized;
  }

  /* Given a dataset object and a prefix, returns a function */
  /* that returns an object containing only the keys -       */
  /* without the prefix - that have the prefix.              */
  /* Example:                                                */
  /* ds = { FooBar: "a", FooBaz: "b", QuuxBar: "c" }         */
  /* dataSetReducer(ds,"foo")                                */
  /* will return                                             */
  /* ds = { bar: "a", baz: "b" }                             */
  static dataSetReducer(dataset, prefix) {
    return (acc, k) => {
      const key = normalizeKey(prefix, k);
      acc[key] = dataset[k];
      return acc;
    }
  };

  /* Given a tag with several data-* attributes with different */
  /* prefixes - i.e: data-lang-x, data-lang-y, data-link-z -   */
  /* and a prefix, returns an array containing only the data-* */
  /* attributes having the given prefix.                       */
  static filterDataset(dataset, prefix) {
    return Object.keys(dataset).filter((s) => s.startsWith(prefix));
  }

  static filterAndReduceDataset(dataset, prefix) {
    return this.filterDataset(dataset, prefix)
      .reduce(this.dataSetReducer(dataset, prefix), {});
  };
}

export default DataSetUtils;
{% endhighlight %}
