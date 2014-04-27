---
layout: post
title: Get the Length of a Video in C#
category: Development
tags: [c#; video, com, shell]
comments: true
share: true
redirect_from: "/get-the-length-of-a-video-in-c/"
---
There are two main ways to get the length of a video file in C#:

<ol>
<li>Use a Shell object (found in shell32.dll in the system32 directory) to get the video length from the file metadata.</li>
<li>Instantiate a WindowsMediaPlayer object (found in wmp.dll if WMP is installed on your machine), load the file into it and then get the length from the corresponding object property.</li>
</ol>
The advantage of the first method is that WMP does not need to be installed on your machine for it to function. This makes it the only option for many servers as well as systems running an "N" edition of Windows which does not contain WMP by default. The second method, however, is simpler to use and on my machine offered a very slight performance advantage (~5%) when looping through a collection of 20 files. Here are both methods:
<a id="more"></a><a id="more-112"></a>

## Method 1 - Shell

*Don't forget to create a reference to shell32.dll found in the system32 folder.*

{% highlight csharp %}
using Shell32
// ...
var shell = new Shell();
var folder = shell.NameSpace(@"filepath");
foreach (FolderItem2 item in folder.Items())
{
    if (item.Name == "filename")
    {
        Console.WriteLine(TimeSpan.FromSeconds(item.ExtendedProperty("System.Media.Duration") / 10000000));
    }
}

Marshal.ReleaseComObject(folder);
Marshal.ReleaseComObject(shell);
{% endhighlight %}

*You may need to apply the [STAThread] attribute to the entry point of your application for this to function properly.*

## Method 2 – WMP

*Don't forget to create a reference to wmp.dll found in the system32 folder.*

{% highlight csharp %}
using WMPLib;
// ...
var player = new WindowsMediaPlayer();
var clip = player.newMedia(filePath);
Console.WriteLine(TimeSpan.FromSeconds(clip.duration));
{% endhighlight %}

