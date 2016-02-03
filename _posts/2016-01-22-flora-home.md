---
layout: post
title: "Flora Time Tracker - Building the home page"
excerpt: "Use pureCss to build a nice and good looking home for the project."
date: 2016-01-26
tags: [Haskell, Stack, PureCss, i18n]
author: ggarciajr
comments: true
---

Yesod's home page comes with few elements to show features like file upload and form processing. Since we don't need them in our home page we are going to remove all the code we don't need.

### Yesod Routing

Yesod provides a DSL for specifying routes with the following syntax: The routes are type-safe and are declared as follows:

```haskell
-- path         handler      methods
/               HomeR        GET POST
```

In the example above we have declared a route **/** which is handled by the controller **HomeR** and that the route accepts both **GET** and **POST** requests.

One thing that is worth noting is that the **routes** file is parsed during compilation and the application won't compile if any problem is found.

There is more to it, but this is enough for now and we'll dig deeper into it as needed.

### Modifying the Home page

We don't need to handle **POST** requests since the home page will only have, for now, links to other pages and information about the project.

We'll first tell Yesod that the **/** route only takes **GET** requests by modifying the **routes** file.

```diff
--- a/config/routes
+++ b/config/routes
@@ -4,4 +4,4 @@
-/ HomeR GET POST
+/ HomeR GET
```

### HTML and CSS

<a href="http://purecss.io/" target="_blank">PureCSS</a> is a set of small, responsive css modules. We are going to use it instead of bootstrap because it has a smaller footprint.

We can delete these three files.

~~~
static/bootstrap.css
templates/homepage.julius
templates/homepage.lucius
~~~

<a href="http://www.yesodweb.com/book/shakespearean-templates#shakespearean-templates_julius_javascript" target="_blank">Julius</a> and <a href="http://www.yesodweb.com/book/shakespearean-templates#shakespearean-templates_lucius_css" target="_blank">Lucius</a> are, basically, javascript and CSS with a some extra features added by yesod.

These extra features can be used, for example, to add static assets in the CSS or type-safe URL in the javascript files that can be checked during the compilation time.

With such features, we can be sure that the application will not compile in case of missing files or if we try to render a link to an unexisting route.

#### Foundation

Lets tell Yesod to support i18n by adding a call to the **mkMessage** function at the end of the **Foundation.hs** file.

```diff
--- a/Foundation.hs
+++ b/Foundation.hs
@@ -65,14 +65,7 @@ instance Yesod App where
         -- you to use normal widget features in default-layout.

         pc <- widgetToPageContent $ do
-            addStylesheet $ StaticR css_bootstrap_css
-
-            -- this is an array of static js files that yesod will
-            -- combine into one single js file and load it via a
-            -- <script> tag.
-            $(combineScripts 'StaticR
-             [ js_bundle_js
-             ])
+
             $(widgetFile "default-layout")
         withUrlRenderer $(hamletFile "templates/default-layout-wrapper.hamlet")

@@ -161,3 +154,6 @@ unsafeHandler = Unsafe.fakeHandlerGetLogger appLogger
 -- https://github.com/yesodweb/yesod/wiki/Sending-email
 -- https://github.com/yesodweb/yesod/wiki/Serve-static-files-from-a-separate-domain
 -- https://github.com/yesodweb/yesod/wiki/i18n-messages-in-the-scaffolding
+
+-- Add I18N support
+mkMessage "App" "messages" "en"
```

#### Layout wrapper

Next, we need to modify the **templates/default-layout-wrapper.hamlet** file, which is the base of the application layout since it includes everything that is commom to all the pages.

Yesod will insert the html and content of each page in this file in the place where we put the call to the **pageBody** function.

```haskell
^{pageBody pc}
```

I'm not going to put the html changes in here, you can see it it on <a href="https://github.com/ggarciajr/flora/commit/7864f7d88782e531d89510deda2c1a7878706e2a" target="_blank">github</a>.

Note that you can reference routes with this construction - **@{FaviconR}** and compilation will fail if there is no **FaviconR** route defined.

#### i18n

Two files will be created to demonstrate how Yesod handles internacionalization. One for english and one for portuguese. These files will be saved inside a folder called **messages** in the project's root folder.

The english file will be name **en.msg** and the portuguese one will be named **pt-BR.msg**. As you can see, to add support for a given language you just need to create a message file for it following the convention **\<lg>-\<CC>.msg** where **lg** is the two char long code for the language and **CC** is the two char long country code variation.

The base syntax for the message files is one ***\<key>: \<value>** pair per line:

~~~
Foo: something
Bar: another text
~~~

#### The home controller.

There are two changes in the home controller. One for remove everything that we'll won't need and the other to tell Yesod to get the title of the application from the message file.

```diff
--- a/Handler/Home.hs
+++ b/Handler/Home.hs
@@ -1,9 +1,6 @@
 module Handler.Home where

-import Import
-import Yesod.Form.Bootstrap3 (BootstrapFormLayout (..), renderBootstrap3,
-                              withSmallInput)
-
+import           Import
 -- This is a handler function for the GET request method on the HomeR
 -- resource pattern. All of your resource patterns are defined in
 -- config/routes
@@ -13,28 +10,8 @@ import Yesod.Form.Bootstrap3 (BootstrapFormLayout (..), renderBootstrap3,
 -- inclined, or create a single monolithic file.
 getHomeR :: Handler Html
 getHomeR = do
-    (formWidget, formEnctype) <- generateFormPost sampleForm
-    let submission = Nothing :: Maybe (FileInfo, Text)
-        handlerName = "getHomeR" :: Text
-    defaultLayout $ do
-        aDomId <- newIdent
-        setTitle "Welcome To Yesod!"
-        $(widgetFile "homepage")
-
-postHomeR :: Handler Html
-postHomeR = do
-    ((result, formWidget), formEnctype) <- runFormPost sampleForm
-    let handlerName = "postHomeR" :: Text
-        submission = case result of
-            FormSuccess res -> Just res
-            _ -> Nothing
+    master <- getYesod

     defaultLayout $ do
-        aDomId <- newIdent
-        setTitle "Welcome To Yesod!"
+        setTitleI MsgTitle
         $(widgetFile "homepage")
-
-sampleForm :: Form (FileInfo, Text)
-sampleForm = renderBootstrap3 BootstrapBasicForm $ (,)
-    <$> fileAFormReq "Choose a file"
-    <*> areq textField (withSmallInput "What's on the file?") Nothing
```

To change the title we used the **setTitleI** function passing **MsgTitle** as argument. **setTitle** is the function uses to set the value of the **\<title>** tag and it takes a text as argument.

**setTitleI** in the other hand is the language sensitive version of **setTitle** and, instead taking a text as argument, it takes a key that must exist in the message file.

So what **setTitleI** does is to look for the text represented by the key used as argument in the message file.

The key is prefixed by **Msg**. So, if you have a key called **Title** you need to pass **MsgTitle** to the **setTitleI** function.

The last thing is that the language will be chosen according to the language set by the user browser.

##### Chosing the page templates

We can tell yesod what template (hamlet file) we want to inject in the **templates/default-layout-wrapper.hamlet** file by using the **widgetFile** function.

This function takes the name of a hamlet file as argument. In the example below, we are telling Yesod to render the content of the **homepage.hamlet** file where the call to **^{pageBody pc}** is.

```haskell
$(widgetFile "homepage")
```

##### homepage.hamlet

For now we'll turn **hompage.hamlet** into an empty file and start building the new home page in the next post.
