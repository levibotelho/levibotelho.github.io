---
layout: post
title: SEO for single page apps without additional processes - Spoon Standalone
category: Open Source
tags: [seo, open source, spoon standalone, angularjs]
comments: true
share: true
---

A month and a half ago I released Spoon; a class library which automates search engine optimisation of single page web applications. Spoon works by using PhantomJS to take snapshots of dynamically-generated web pages and then serves these snapshots to web crawlers when they request to see the dynamic content. Spoon is automatic, self-contained, easy to set up and it works really well. However, it requires that your application have the right to launch a new process...

The problem is that not all web applications do have this right. A web application running as an Azure Web Site, for example, cannot launch additional processes. Without this ability Spoon is unable to generate page snapshots on-demand and is therefore unable to do its job.

So that's why I wrote Spoon Standalone. Spoon Standalone does the same thing as Spoon, just a little differently. Instead of running as a part of a given web application, it is run on its own once an application is published. Instead of storing snapshots in the folder hierarchy of the web application it crawls, it stores them remotely and independently in Azure blob storage. Instead of being written in C# and exclusively for ASP.NET, it is written in Node.js and is compatible with all server-side web application frameworks. And don't worry if you're not a Node developer. No knowledge of Node.js is required to use it.
 
## How it is used

1. You download Spoon Standalone and configure it to use a certain Azure blob storage container and to crawl a collection of URLs. These can be configured manually, but Spoon Standalone also knows how to read simple sitemaps.
2. You hook code into your web application to retrieve a page snapshot from your Azure blob storage container when a crawler makes a request using an `_escaped_fragment_` parameter. If you're developing an ASP.NET MVC site, then you simply grab the Spoon Standalone Connector off of NuGet (`PM> Install-Package Spoon.Standalone.Connector`) which handles this step for you.
3. You publish your application.
4. You launch Spoon Standalone, which fills your blob storage container with snapshots of all the pages you asked it to crawl. Execution completes, and you're done!

The process of acquiring, configuring and using Spoon Standalone is fully documented on the project's [GitHub page](https://github.com/LeviBotelho/spoon-standalone).