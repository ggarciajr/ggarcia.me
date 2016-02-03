---
layout: post
title: "Flora Time Tracker - Finishing the home page"
excerpt: "Build the first React component and show how to integrate it with the application."
date: 2016-01-27
tags: [Haskell, Stack, Test, React, Webpack, Karma, Npm]
author: ggarciajr
comments: true
---

To code the new home page we are going to need:

 * Create the language content in both files **en.msg** and **pt-BR.msg**.
 * Create the navigation bar that will be a React component.
 * Test the navigation component using Mocha, Expect, and Karma.
 * Test the Yesod home controller using HSpec.

You can find the related changes on <a href="https://github.com/ggarciajr/flora/commit/4cbdc19c050c4a1910eaeb9cc4e909d468e9e4d9" target="_blank">github</a>.

### Hamlet template and i18n

To use i18n inside hamlet templates we need to refer to a **\<prefixKey>** using the following syntax:

```haskell
-- will render the value of the Title key for a given language.
_{MsgTitle}
```

Each key used in the hamlet file needs to be inserted in both **en.msg** and **pt-BR.msg** files otherwise the application will not compile.

### React component and i18n

The react components are outside of the Yesod rendering scope. This means that we cannot use **_{MsgSomeKey}** to get the internationalized text.

The solution is to render the text needed by the component as **data-*** atributes of the html tag that will be the component container and pass the dataset attribute of the container as a property of the React component.

```diff
+<div class="header" id="header"
+     data-title=_{MsgTitle}
+     data-home=_{MsgNavigationHome}
+     data-try-free="_{MsgNavigationTryFree}"
+     data-sign-up="_{MsgNavigationSignUp}"
+     data-sign-in="_{MsgNavigationSignIn}">
+  <!--- navigation will be rendered here -->
```

### Webpack and Saas

Webpack only understands javascript and in order to make it compile Saas files we need to **require** the Saas file in a javascript file.

However, this will generate the css inside the javascript file. To extract the css from the javascript file into css files, we need to use a webpack plugin called **extract-text-webpack-pluginextract-text-webpack-plugin**.

```diff
--- a/webpack.config.js
+++ b/webpack.config.js
@@ -1,4 +1,5 @@
 var path = require('path');
+var ExtractTextPlugin = require('extract-text-webpack-plugin');

 module.exports = {
   //The entry point for the bundle.
@@ -9,4 +10,18 @@ module.exports = {
     //Locatian of the artifact - ./static/js
     path: path.join(__dirname, 'static', 'js')
   }
+  ,
+  module: {
+    loaders: [
+      { test: /\.scss$/, loader: ExtractTextPlugin.extract('css!sass') },
+      { test: /\.js?$/,
+        loader: 'babel-loader',
+        exclude: /node_modules|static/,
+        query: { presets: ['es2015', 'react'] }
+      }
+    ]
+  },
+  plugins: [
+    new ExtractTextPlugin('../css/style.css', {allChunks: true})
+  ]
 };
```

### Testing with Karma

The configuration of the test suite is composed by two files - **test.webpack.js** and **karma.conf.js** - The later is to configure Karma and to integrate it with Webpack. The former is to tell webpack that every file ending in **.test.js** is part of a test suite.

The last thing to do is to add a script block in the **package.json** to tell **npm** to start karma when running the tests.

```json
"scripts": {
  "test": "karma start"
}
```

To run the tests we run the command below:

```shell
-- runs the test suite.
npm test
```

### Testing with HSpec

To run the hspec tests we run the following command:

```shell
-- runs the test suite.
stack test
```

### Rendering the React component

To add the navigation component in the home page we need to modify the **index.js** file as follows:

```diff
--- a/js/index.js
+++ b/js/index.js
@@ -1,4 +1,14 @@
+import React from 'react';
+import ReactDOM from 'react-dom';
+import Navigation from './components/navigation.js';
+
 (function() {
   'use strict';
-  console.log("webpack is working.");
+  // makes Webpack to compile the scss
+  require('../scss/main.scss');
+  const navigationContainer = document.getElementById('header');
+  ReactDOM.render(
+    <Navigation language={navigationContainer.dataset}/>,
+    navigationContainer
+  );
```

### Making Yesod aware of the css and javascript files

Webpack will create two files - **static/css/style.css** and **static/js/bundle.js** - and we need to modify the **Foundation.hs** file to configure Yesod to include those files in the final html.

```diff
--- a/Foundation.hs
+++ b/Foundation.hs
@@ -65,7 +65,21 @@ instance Yesod App where
         -- you to use normal widget features in default-layout.

         pc <- widgetToPageContent $ do
-
+            -- add CDN-hosted
+            addStylesheetRemote "https:////maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css"
+            addStylesheetRemote "https://fonts.googleapis.com/css?family=Roboto:400,300"
+
+            -- Takes a list of css files and generate one big css file.
+            $(combineStylesheets 'StaticR
+             [ css_style_css
+             ])
+
+            -- this is an array of static js files that yesod will
+            -- combine into one single js file and load it via a
+            -- <script> tag.
+            $(combineScripts 'StaticR
+             [ js_bundle_js
+             ])
             $(widgetFile "default-layout")
         withUrlRenderer $(hamletFile "templates/default-layout-wrapper.hamlet")
```

### Final result

This is what you should see after starting the application and accessing <a href="http://localhost:3000" target="_blank">http://localhost:3000</a> on your browser.

<img src="/img/posts/flora-final-home.png"/>
