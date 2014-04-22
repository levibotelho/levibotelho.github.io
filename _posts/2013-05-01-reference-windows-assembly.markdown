---
layout: post
status: publish
published: true
title: Reference the Windows Assembly in a C# Project
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
wordpress_id: 422
wordpress_url: http://levibotelho.azurewebsites.net/?p=422
date: !binary |-
  MjAxMy0wNS0wMSAyMDoxMjowNiArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNS0wMSAxODoxMjowNiArMDIwMA==
categories:
- C#
tags:
- windows
- assembly
comments: []
---
<p>The case may arise where you want to include the Windows assembly, as used in Windows 8 apps, in a project which doesn't reference it by default. Referencing this assembly isn't as trivial a task as it may seem, as the Windows assembly simply doesn’t appear in the list of available assemblies in the Reference Manager in most projects. The reason for this is that the assembly is only available for reference in applications which specifically target Windows 8. In order to make this the case for your project, you need to make a small modification to your.csproj file. In the first “PropertyGroup” element of your project file, add the following XML tag:</p>
<p>[sourcecode language="xml"]</p>
<p>&lt;TargetPlatformVersion&gt;8.0&lt;/TargetPlatformVersion&gt;</p>
<p>[/sourcecode]</p>
<p>Personally, I like to add it just under the “TargetFrameworkVersion” element for clarity.</p>
<p>Now, save your project file, re-open the Reference Manager, and you’ll see that a new "Windows" tab magically appears. Click the tab, and you'll see that the Windows assembly is now available for reference.</p>
