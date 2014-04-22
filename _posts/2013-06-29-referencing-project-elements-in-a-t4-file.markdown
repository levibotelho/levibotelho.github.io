---
layout: post
status: publish
published: true
title: Referencing project elements in a T4 file
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "<em>Inspired by <a title=\"Stack Overflow - T4 reference a const in a
  static class at compile time\" href=\"http://stackoverflow.com/q/17123991/1068266\"
  target=\"_blank\">this Stack Overflow question</a>. Give the asker an upvote if
  you have a minute.</em>\r\n\r\n<hr />\r\n\r\nIt often occurs when writing a T4 template
  that you need to reference some element of the project containing the template.
  However, simply trying to reference a variable or call a function can be very frustrating.
  Especially because the current lack of T4 IntelliSense requires you to regenerate
  your code over and over again, only to find out that the “type or namespace name
  could not be found”.\r\n\r\nThe reason why this is so frustrating is because of
  a natural mistake people make, which is thinking that a T4 file is just like any
  other code file in your project."
wordpress_id: 1552
wordpress_url: http://www.levibotelho.com/?p=1552
date: !binary |-
  MjAxMy0wNi0yOSAxMDo1OToxMCArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNi0yOSAwODo1OToxMCArMDIwMA==
categories:
- Metaprogramming
- C#
tags:
- c#
- t4
comments: []
---
<p><em>Inspired by <a title="Stack Overflow - T4 reference a const in a static class at compile time" href="http://stackoverflow.com/q/17123991/1068266" target="_blank">this Stack Overflow question</a>. Give the asker an upvote if you have a minute.</em></p>
<hr />
<p>It often occurs when writing a T4 template that you need to reference some element of the project containing the template. However, simply trying to reference a variable or call a function can be very frustrating. Especially because the current lack of T4 IntelliSense requires you to regenerate your code over and over again, only to find out that the “type or namespace name could not be found”.</p>
<p>The reason why this is so frustrating is because of a natural mistake people make, which is thinking that a T4 file is just like any other code file in your project.<a id="more"></a><a id="more-1552"></a> This isn’t true. A better way to think of a .tt is as an independent set of instructions used to drop a generated file into your project when it is run. Because the .tt is independent, &lt;#= ConsoleApplication1.MyClass.MyVariable #&gt; is meaningless without a reference to the assembly containing <em>ConsoleApplication1.MyClass.MyVariable</em>. To create this reference, add the following include statement to your T4:</p>
<p>[csharp]<br />
&lt;#@ assembly name=&quot;$(TargetPath)&quot; #&gt;<br />
[/csharp]</p>
<p>Don’t forget the namespace:</p>
<p>[csharp]<br />
&lt;#@ import namespace=&quot;ConsoleApplication2&quot; #&gt;<br />
[/csharp]</p>
<p>Now you’ll be able to reference public elements of your project. But there is one more “gotcha”. Because your .tt is referencing your project assembly, if your assembly is out of date or simply hasn’t been built yet, your T4 won’t generate correctly. This is especially frustrating when your generated file has compilation errors, because you’ll end up in a deadlock when your noncompiling generated file is preventing you from correcting the errors contained within it. The solution to this is to manually make just enough corrections in the generated file to eliminate all compile time errors and then rebuild your project. You can then regenerate your file which will hopefully be now free of errors and then rebuild the project once more to take into account the generated code.</p>
