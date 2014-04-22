---
layout: post
status: publish
published: true
title: Team Foundation Service Error TF30063
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "<em>Preamble: For those not familiar with Team Foundation Service, it
  really is an exceptional product (TFS hosted in the cloud for free!). See <a href=\"http://tfs.visualstudio.com/\">http://tfs.visualstudio.com/</a> for
  more information.</em>\r\n\r\nI received this perplexing error message when opening
  a solution in a Visual Studio instance opened via the \"Open a new instance of Visual
  Studio\" link in a TF Service project:\r\n<blockquote>TF30063: You are not authorized
  to access [project]</blockquote>\r\nThere are a good number proposed solutions out
  there for this error, which include clearing your internet browser's cookies, and
  reinitialising IE settings to their factory defaults. I had to try quite a few things
  until I stumbled upon this simple solution to the problem:\r\n"
wordpress_id: 672
wordpress_url: http://www.levibotelho.com/?p=672
date: !binary |-
  MjAxMy0wNi0wMiAxODowMjowNCArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNi0wMiAxNjowMjowNCArMDIwMA==
categories:
- TFS
tags:
- tfs
- visual studio
comments:
- id: 20232
  author: Michael Grimm
  author_email: michael@decisivedata.net
  author_url: http://decisivedata.net
  date: !binary |-
    MjAxMy0xMC0wMyAxOTo0Njo1OCArMDIwMA==
  date_gmt: !binary |-
    MjAxMy0xMC0wMyAxNzo0Njo1OCArMDIwMA==
  content: Thank you, thank you, thank you. This is really painful if you have multiple
    Microsoft Accounts that you need to use! Your solution works great!
- id: 20262
  author: Levi
  author_email: levi_botelho@outlook.com
  author_url: ''
  date: !binary |-
    MjAxMy0xMC0wNCAwOTo0MzoyNCArMDIwMA==
  date_gmt: !binary |-
    MjAxMy0xMC0wNCAwNzo0MzoyNCArMDIwMA==
  content: Glad to help! Very frustrating error, I know ;).
---
<p><em>Preamble: For those not familiar with Team Foundation Service, it really is an exceptional product (TFS hosted in the cloud for free!). See <a href="http://tfs.visualstudio.com/">http://tfs.visualstudio.com/</a> for more information.</em></p>
<p>I received this perplexing error message when opening a solution in a Visual Studio instance opened via the "Open a new instance of Visual Studio" link in a TF Service project:</p>
<blockquote><p>TF30063: You are not authorized to access [project]</p></blockquote>
<p>There are a good number proposed solutions out there for this error, which include clearing your internet browser's cookies, and reinitialising IE settings to their factory defaults. I had to try quite a few things until I stumbled upon this simple solution to the problem:<br />
<a id="more"></a><a id="more-672"></a></p>
<ol>
<li>Open Visual Studio.</li>
<li>Open the integrated web browser (VIEW -&gt; Other Windows -&gt; Web Browser).</li>
<li>Navigate to your project url (xxx.visualstudio.com).</li>
<li>Log out (if necessary) and log in with the correct Microsoft account.</li>
<li>Open the project you want to work with, and then click the "Open a new instance of Visual Studio" link</li>
</ol>
<p>The error shouldn't appear in the new instance of Visual Studio that opens. Quick, easy, and no painful IE resets. Hope this can help somebody!</p>
