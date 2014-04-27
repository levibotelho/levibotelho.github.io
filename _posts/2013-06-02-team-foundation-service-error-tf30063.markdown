---
layout: post
title: Team Foundation Service Error TF30063
category: Development
tags: [tfs, visual studio]
comments: true
share: true
---

I received this perplexing error message when opening a solution in a Visual Studio instance opened via the "Open a new instance of Visual Studio" link in a TF Service project:

> TF30063: You are not authorized to access [project]

There are a good number proposed solutions out there for this error, which include clearing your internet browser's cookies, and reinitialising IE settings to their factory defaults. I had to try quite a few things until I stumbled upon this simple solution to the problem:

+ Open Visual Studio.
+ Open the integrated web browser (VIEW -> Other Windows -> Web Browser).
+ Navigate to your project url (xxx.visualstudio.com).
+ Log out (if necessary) and log in with the correct Microsoft account.
+ Open the project you want to work with, and then click the "Open a new instance of Visual Studio" link

The error shouldn't appear in the new instance of Visual Studio that opens. Quick, easy, and no painful IE resets. Hope this can help somebody!