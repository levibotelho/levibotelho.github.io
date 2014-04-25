---
layout: post
title: Team Foundation Service Error TF30063
categories: [Visual Studio]
tags:[tfs, visual studio]
comments: true
share: true
---
*Preamble: For those not familiar with Team Foundation Service, it really is an exceptional product (TFS hosted in the cloud for free!). See [http://tfs.visualstudio.com/](http://tfs.visualstudio.com/) for more information.*

I received this perplexing error message when opening a solution in a Visual Studio instance opened via the "Open a new instance of Visual Studio" link in a TF Service project:

<blockquote>TF30063: You are not authorized to access [project]
</blockquote>
There are a good number proposed solutions out there for this error, which include clearing your internet browser's cookies, and reinitialising IE settings to their factory defaults. I had to try quite a few things until I stumbled upon this simple solution to the problem:
<a id="more"></a><a id="more-672"></a>

<ol>
<li>Open Visual Studio.</li>
<li>Open the integrated web browser (VIEW -> Other Windows -> Web Browser).</li>
<li>Navigate to your project url (xxx.visualstudio.com).</li>
<li>Log out (if necessary) and log in with the correct Microsoft account.</li>
<li>Open the project you want to work with, and then click the "Open a new instance of Visual Studio" link</li>
</ol>
The error shouldn't appear in the new instance of Visual Studio that opens. Quick, easy, and no painful IE resets. Hope this can help somebody!

