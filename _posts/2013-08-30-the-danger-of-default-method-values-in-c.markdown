---
layout: post
status: publish
published: true
title: The danger of default method values in C#
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "While seemingly benign, methods containing default parameter values can
  be the cause of mysterious errors in deployed applications. To see why, let’s define
  a new class library called “ConsoleWriter” and add to it the following code.\r\n\r\n[csharp]\r\npublic
  static class ConsoleWriter\r\n{\r\n    public static void WriteMessage(string message
  = &quot;Hello World&quot;)\r\n    {\r\n        Console.WriteLine(message);\r\n    }\r\n}\r\n[/csharp]\r\n\r\nPretty
  run-of-the-mill stuff. Let’s compile this DLL and reference it in a new solution
  which will call WriteMessage.\r\n\r\n[csharp]\r\nstatic void Main(string[] args)\r\n{\r\n
  \   ConsoleWriter.ConsoleWriter.WriteMessage();\r\n    Console.ReadLine();\r\n}\r\n[/csharp]\r\n\r\nNow
  let's build the project and run the generated executable (in the bin folder). As
  expected, “Hello World” is printed to the console window. But now let’s go back
  to our ConsoleWriter project and change our default message from “Hello World” to
  “Goodbye World” and rebuild the DLL.\r\n"
wordpress_id: 1792
wordpress_url: http://www.levibotelho.com/?p=1792
date: !binary |-
  MjAxMy0wOC0zMCAxMzoxMDo0NiArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wOC0zMCAxMToxMDo0NiArMDIwMA==
categories:
- C#
tags:
- c#
comments:
- id: 17492
  author: Benoit Blanchon
  author_email: benoit.blanchon@gmail.com
  author_url: ''
  date: !binary |-
    MjAxMy0wOS0xNSAxMzo1NDoxOCArMDIwMA==
  date_gmt: !binary |-
    MjAxMy0wOS0xNSAxMTo1NDoxOCArMDIwMA==
  content: ! "Thank you Levi for this great article !\r\n\r\nI think it's worth mentioning
    that you have the exact same issue with <code>const</code> (as you stated in a
    previous article) and with <code>enum</code> : the value is compiled in the caller's
    IL, just like a good old <code>#define</code> in C."
---
<p>While seemingly benign, methods containing default parameter values can be the cause of mysterious errors in deployed applications. To see why, let’s define a new class library called “ConsoleWriter” and add to it the following code.</p>
<p>[csharp]<br />
public static class ConsoleWriter<br />
{<br />
    public static void WriteMessage(string message = &quot;Hello World&quot;)<br />
    {<br />
        Console.WriteLine(message);<br />
    }<br />
}<br />
[/csharp]</p>
<p>Pretty run-of-the-mill stuff. Let’s compile this DLL and reference it in a new solution which will call WriteMessage.</p>
<p>[csharp]<br />
static void Main(string[] args)<br />
{<br />
    ConsoleWriter.ConsoleWriter.WriteMessage();<br />
    Console.ReadLine();<br />
}<br />
[/csharp]</p>
<p>Now let's build the project and run the generated executable (in the bin folder). As expected, “Hello World” is printed to the console window. But now let’s go back to our ConsoleWriter project and change our default message from “Hello World” to “Goodbye World” and rebuild the DLL.<br />
<a id="more"></a><a id="more-1792"></a></p>
<p>[csharp]<br />
public static class ConsoleWriter<br />
{<br />
    public static void WriteMessage(string message = &quot;Goodbye World&quot;)<br />
    {<br />
        Console.WriteLine(message);<br />
    }<br />
}<br />
[/csharp]</p>
<p>Now let’s re-execute the .exe we built earlier. Surprisingly, we see that when we run our executable “Hello World” is still printed to the console window!<br />
To understand how this can be, we need to look at the IL for the Main method in our executable.</p>
<p>[sourcecode]<br />
.method private hidebysig static void  Main(string[] args) cil managed<br />
{<br />
  .entrypoint<br />
  // Code size       17 (0x11)<br />
  .maxstack  8<br />
  IL_0000:  ldstr      &quot;Hello World.&quot;<br />
  IL_0005:  call       void [ConsoleWriter]ConsoleWriter.ConsoleWriter::WriteMessage(string)<br />
  IL_000a:  call       string [mscorlib]System.Console::ReadLine()<br />
  IL_000f:  pop<br />
  IL_0010:  ret<br />
} // end of method Program::Main<br />
[/sourcecode]</p>
<p>Notice how “Hello World” is embedded directly in the IL. <em>When calling a method in another module (as we are doing here) the default values for parameters are embedded in the calling code.</em> This means that if we go back and modify the method’s default value without recompiling the calling application, the value change will not be taken into account.* ** While this scenario is rarely encountered during the development phase of an application, it has the potential to occur frequently in production environments where it is common to upgrade a single DLL without redelivering an entire solution. The fact that you will only run into this issue in very rare cases during development makes it all the more dangerous, as relatively few developers become aware of the problem to begin with.<br />
There is however, a simple way to avoid it. In our case, all we need to do to fix our WriteMessage code is by doing the following.</p>
<p>[csharp]<br />
public static void WriteMessage(string message = null)<br />
{<br />
    if (message == null)<br />
        message = &quot;Goodbye World.&quot;;</p>
<p>    Console.WriteLine(message);<br />
}<br />
[/csharp]</p>
<p>Now, while null will still be embedded in calling modules as a default value, the real default value of the method (which in this case is “Goodbye World”) will not be. If we later want to change “Goodbye World” to something else, we can do so and redeliver our ConsoleWriter DLL without having to worry that a “Goodbye World” is still lurking somewhere in a calling module.</p>
<hr />
<p><em>* If you followed along with this demonstration but got lazy and threw both projects in a single solution, or launched the console application via the VS debugger, it would have been recompiled and you would not have arrived at the same result as I have here.</em></p>
<p>** This might remind you of <a title="The difference between const and readonly in C#" href="http://www.levibotelho.com/the-difference-between-const-and-readonly-in-c/" target="_blank">a similar topic that I wrote about last month</a>.</p>
