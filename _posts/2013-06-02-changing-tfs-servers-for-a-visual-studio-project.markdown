---
layout: post
status: publish
published: true
title: Changing TFS Servers for a Visual Studio Project
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
wordpress_id: 762
wordpress_url: http://www.levibotelho.com/?p=762
date: !binary |-
  MjAxMy0wNi0wMiAxOTo0MjozMiArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNi0wMiAxNzo0MjozMiArMDIwMA==
categories:
- Visual Studio
- TFS
tags:
- tfs
- visual studio
comments: []
---
<p>I recently had to change a project over from TF Service to Codeplex (more on this project in a coming post!) and found that the process required to change the source control server of a project in Visual Studio isn't necessarily intuitive. Here's how it's done:</p>
<ol>
<li>Open the "Workspaces" dialog (FILE -&gt; Source Control -&gt; Advanced -&gt; Workspaces...)</li>
<li>Click the "Edit" button to edit the workspace containing the information for the server you want to get rid of.</li>
<li>Remove the entry which points to the project you want to change source control for.</li>
<li>Close the dialogs, and then connect to your new TFS instance by going to TEAM -&gt; Connect to Team Foundation Server.</li>
<li>Add your new solution to the TFS instance (FILE -&gt; Source Control -&gt; Add Solution to Source Control...)</li>
</ol>
<p>That's all there is to it. As a reminder, don't forget that this doesn't actually delete anything from the former TFS instance, it just moves your project from one server to another. That means that if you don't want your code staying on the old server indefinitely, you'll have to go and remove it yourself.</p>
