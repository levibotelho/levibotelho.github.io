---
layout: post
status: publish
published: true
title: Get the Length of a Video in C#
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "There are two main ways to get the length of a video file in C#:\r\n<ol>\r\n\t<li>Use
  a Shell object (found in shell32.dll in the system32 directory) to get the video
  length from the file metadata.</li>\r\n\t<li>Instantiate a WindowsMediaPlayer object
  (found in wmp.dll if WMP is installed on your machine), load the file into it and
  then get the length from the corresponding object property.</li>\r\n</ol>\r\nThe
  advantage of the first method is that WMP does not need to be installed on your
  machine for it to function. This makes it the only option for many servers as well
  as systems running an \"N\" edition of Windows which does not contain WMP by default.
  The second method, however, is simpler to use and on my machine offered a very slight
  performance advantage (~5%) when looping through a collection of 20 files. Here
  are both methods:\r\n"
wordpress_id: 112
wordpress_url: http://levibotelho.azurewebsites.net/?p=112
date: !binary |-
  MjAxMy0wNC0xNyAxMDozMToxOSArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODozMToxOSArMDIwMA==
categories:
- C#
tags:
- c#
- video
- com
- shell
comments: []
---
<p>There are two main ways to get the length of a video file in C#:</p>
<ol>
<li>Use a Shell object (found in shell32.dll in the system32 directory) to get the video length from the file metadata.</li>
<li>Instantiate a WindowsMediaPlayer object (found in wmp.dll if WMP is installed on your machine), load the file into it and then get the length from the corresponding object property.</li>
</ol>
<p>The advantage of the first method is that WMP does not need to be installed on your machine for it to function. This makes it the only option for many servers as well as systems running an "N" edition of Windows which does not contain WMP by default. The second method, however, is simpler to use and on my machine offered a very slight performance advantage (~5%) when looping through a collection of 20 files. Here are both methods:<br />
<a id="more"></a><a id="more-112"></a></p>
<h2>Method 1 - Shell</h2>
<p><em>Don't forget to create a reference to shell32.dll found in the system32 folder.</em></p>
<p>[sourcecode language="csharp"]<br />
using Shell32<br />
// ...<br />
var shell = new Shell();<br />
var folder = shell.NameSpace(@&quot;filepath&quot;);<br />
foreach (FolderItem2 item in folder.Items())<br />
{<br />
    if (item.Name == &quot;filename&quot;)<br />
    {<br />
        Console.WriteLine(TimeSpan.FromSeconds(item.ExtendedProperty(&quot;System.Media.Duration&quot;) / 10000000));<br />
    }<br />
}</p>
<p>Marshal.ReleaseComObject(folder);<br />
Marshal.ReleaseComObject(shell);<br />
[/sourcecode]</p>
<p><em>You may need to apply the [STAThread] attribute to the entry point of your application for this to function properly.</em></p>
<h2>Method 2 – WMP</h2>
<p><em>Don't forget to create a reference to wmp.dll found in the system32 folder.</em></p>
<p>[sourcecode language="csharp"]<br />
using WMPLib;<br />
// ...<br />
var player = new WindowsMediaPlayer();<br />
var clip = player.newMedia(filePath);<br />
Console.WriteLine(TimeSpan.FromSeconds(clip.duration));<br />
[/sourcecode]</p>
