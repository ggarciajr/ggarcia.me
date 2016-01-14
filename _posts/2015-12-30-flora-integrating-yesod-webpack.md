---
layout: post
title: "Flora Time Tracker - Integrating Yesod and Webpack"
excerpt: "Now, we will learn how to set up webpack and how to integrate it with yesod."
date: 2015-12-30
tags: [Time, Time Tracker, Haskell, Yesod, Haskell Web Application, webpack]
author: ggarciajr
comments: true
---

NodeJs is a pre-requisite to install webpack. Check <a href="https://docs.npmjs.com/getting-started/installing-node" target="_blank">here</a> how to install NodeJs
if you don't have it installed already.


Next thing we need to do is to install <a href="http://webpack.github.io/" target="_blank">webpack</a>.
We do it by running the following command:

{% highlight bash %}
npm install webpack
{% endhighlight %}

Once you get webpack installed, it is time to configure it and make yesod aware of the
artifacts produced by it.

To configure webpack we need to add the ***webpack.config.js*** file into the
project's root folder. Here is the file content:

{% highlight javascript %}
//file: $ROOT/webpack.config.js
var path = require('path');

module.exports = {
  //The entry point for the bundle.
  entry: './js/index.js',
  output: {
    //Name of the artifact produced by webpack.
    filename: 'bundle.js',
    //Locatian of the artifact - ./static/js
    path: path.join(__dirname, 'static', 'js')
  }
};
{% endhighlight %}

We'll now create a javascript function that executes itself just to be sure
that webpack is configured and working as expected.

To do so, we need to create a new folder called ***js*** and inside it a file
called ***index.js***

{% highlight javascript %}
//file: $ROOT/js/index.js
(function() {
  'use strict';
  console.log("webpack is working.");
})();
{% endhighlight %}

Lets run the following command in a shell to tell webpack to create the bundle.
Note that the command should be run from the project's root folder.

{% highlight bash %}
webpack
{% endhighlight %}

If all succeed, you should see the ***bundle.js*** file inside the ***static/js***
folder.

Next step is to make yesod to use the ***bundle.js*** file. For this, we need
to modify the ***Foundation.hs*** file as follows:

{% highlight haskell %}
pc <- widgetToPageContent $ do
    addStylesheet $ StaticR css_bootstrap_css

    -- modification start
    -- this is an array of static js files that yesod will
    -- combine into one single js file and load it via a
    -- <script> tag.
    $(combineScripts 'StaticR
    [ js_bundle_js
    ])
    -- modification end

    $(widgetFile "default-layout")
{% endhighlight %}

The last thing is to make yesod aware that of changes in static folder so it
can discover our new ***bundle.js*** file.

{% highlight bash %}
touch Settings/StaticFiles.hs
{% endhighlight %}

Run the project and go to <a href="http://localhost:3000" target="_blank">http://localhost:3000</a>

{% highlight bash %}
stack exec yesod devel
{% endhighlight %}

You should see, in the javascript console of your browser, the sentence: ***webpack is working.***

<img src="/img/posts/webpack-js-console.png"/>
