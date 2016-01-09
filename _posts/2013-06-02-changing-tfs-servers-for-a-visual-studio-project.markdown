---
layout: post
title: Changing TFS Servers for a Visual Studio Project
category: Visual Studio
tags: [visual studio, tfs]
description: Ever needed to migrate a project from one TFS server to another? Here's how you can do it quickly and easily using Visual Studio.
comments: true
share: true
redirect_from: "/changing-tfs-servers-for-a-visual-studio-project/"
---
I recently had to change a project over from TF Service to Codeplex (more on this project in a coming post!) and found that the process required to change the source control server of a project in Visual Studio isn't necessarily intuitive. Here's how it's done:

<ol>
<li>Open the "Workspaces" dialog (FILE -> Source Control -> Advanced -> Workspaces...)</li>
<li>Click the "Edit" button to edit the workspace containing the information for the server you want to get rid of.</li>
<li>Remove the entry which points to the project you want to change source control for.</li>
<li>Close the dialogs, and then connect to your new TFS instance by going to TEAM -> Connect to Team Foundation Server.</li>
<li>Add your new solution to the TFS instance (FILE -> Source Control -> Add Solution to Source Control...)</li>
</ol>
That's all there is to it. As a reminder, don't forget that this doesn't actually delete anything from the former TFS instance, it just moves your project from one server to another. That means that if you don't want your code staying on the old server indefinitely, you'll have to go and remove it yourself.

