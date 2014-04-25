---
layout: post
status: publish
published: true
title: ! '[node.js] The build tools for Visual Studio XXXX cannot be found'
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
wordpress_id: 5511
wordpress_url: http://www.levibotelho.com/?p=5511
date: !binary |-
  MjAxNC0wMy0yOSAxMTo0NjoxNiArMDEwMA==
date_gmt: !binary |-
  MjAxNC0wMy0yOSAxMDo0NjoxNiArMDEwMA==
categories:
- Visual Studio
- node.js
tags:
- visual studio
- node.js
- npm
comments: []
---
I got this error when trying to install a node.js package that wanted to build some associated .cc files with the Visual Studio 2010 compiler (v100 build tools). I haven't had Visual Studio 2010 installed on my machine since Visual Studio 2012 came out, and *really* didn't want to reinstall it.

It turns out that the solution to this is quite simple. You simply need to add the following argument to your install command.

{% highlight powershell %}
--msvs_version=2013
[/powershell]

Your install command will therefore look like this.

{% highlight powershell %}
npm install [pkg] -S --msvs_version=2013
[/powershell]

