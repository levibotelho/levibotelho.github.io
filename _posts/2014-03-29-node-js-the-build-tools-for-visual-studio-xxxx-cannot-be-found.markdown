---
layout: post
title: ! '[Node.js] The build tools for Visual Studio XXXX cannot be found'
category: Development
tags: [visual studio, node.js, npm]
description: If you develop NodeJS on Windows, you've probably run into this annoying error. Luckily, the fix is really simple. 
comments: true
share: true
redirect_from: "/node-js-the-build-tools-for-visual-studio-xxxx-cannot-be-found/"
---
I got this error when trying to install a node.js package that wanted to build some associated .cc files with the Visual Studio 2010 compiler (v100 build tools). I haven't had Visual Studio 2010 installed on my machine since Visual Studio 2012 came out, and *really* didn't want to reinstall it.

It turns out that the solution to this is quite simple. You simply need to add the following argument to your install command.

{% highlight bash %}
--msvs_version=2013
{% endhighlight %}

Your install command will therefore look like this.

{% highlight bash %}
npm install [pkg] -S --msvs_version=2013
{% endhighlight %}

