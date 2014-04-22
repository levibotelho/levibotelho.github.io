---
layout: post
status: publish
published: true
title: What is string interning?
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "Execute this block of code in a new console application:\r\n\r\n[sourcecode
  language=\"csharp\"]\r\nvar array1 = new[] { 10 };\r\nvar array2 = new[] { 10 };\r\nConsole.WriteLine(ReferenceEquals(array1, array2));\r\nConsole.ReadLine();\r\n[/sourcecode]\r\n\r\nMost
  people would expect it to print “False”, which is exactly what it does. All that
  you’ve done is create two new integer arrays and then tested to see if their references
  on the stack are the same, which they logically are not.\r\n\r\nNow execute this
  block of code:\r\n\r\n[sourcecode language=\"csharp\"]\r\nvar string1 = &quot;Hello world&quot;;\r\nvar string2 = &quot;Hello world&quot;;\r\nConsole.WriteLine(ReferenceEquals(string1, string2));\r\nConsole.ReadLine();\r\n[/sourcecode]\r\n\r\nThere
  is a distinct possibility (especially If you are running .NET 4.5) that it will
  print “true”. What’s going on?\r\n"
wordpress_id: 532
wordpress_url: http://levibotelho.azurewebsites.net/?p=532
date: !binary |-
  MjAxMy0wNS0yNCAxNjo1NToyOSArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNS0yNCAxNDo1NToyOSArMDIwMA==
categories:
- C#
tags:
- c#
- clr
comments: []
---
<p>Execute this block of code in a new console application:</p>
<p>[sourcecode language="csharp"]<br />
var array1 = new[] { 10 };<br />
var array2 = new[] { 10 };<br />
Console.WriteLine(ReferenceEquals(array1, array2));<br />
Console.ReadLine();<br />
[/sourcecode]</p>
<p>Most people would expect it to print “False”, which is exactly what it does. All that you’ve done is create two new integer arrays and then tested to see if their references on the stack are the same, which they logically are not.</p>
<p>Now execute this block of code:</p>
<p>[sourcecode language="csharp"]<br />
var string1 = &quot;Hello world&quot;;<br />
var string2 = &quot;Hello world&quot;;<br />
Console.WriteLine(ReferenceEquals(string1, string2));<br />
Console.ReadLine();<br />
[/sourcecode]</p>
<p>There is a distinct possibility (especially If you are running .NET 4.5) that it will print “true”. What’s going on?<br />
<a id="more"></a><a id="more-532"></a><br />
This is the result of string interning. String interning is a process whereby a single copy of a given string is stored in application memory, and referenced everywhere where a new copy of the same string would otherwise be created. The most obvious advantage to doing this is that it prevents the same string from being stored in memory multiple times, however it also provides a performance boost by making string comparisons significantly easier. Two variables holding the same interned string will forcibly contain the same reference.</p>
<h2>How does it work?</h2>
<p><span style="line-height: 1.6em;">Interning in .NET is managed with the help of a hashtable. When the CLR wants to intern a string, it hashes it and checks the resulting value against the hashtable to see if the string has already been interned. If is has, the string variable's reference is updated to point to the interned version of the string. If it hasn't yet been interned, it creates a new entry in the intern table and the reference is updated accordingly.</span></p>
<h2>When are strings interned?</h2>
<p>The answer isn’t exactly simple. The CLR will by default intern all string literals found in your application, but the C# compiler overrides this behaviour by default because this interning results in a performance hit when the application is started. Even then however, the CLR can still choose to intern.</p>
<p>The only bulletproof way to ensure that a given string is interned is by interning it yourself, with the string.Intern() method. You can also use the string.IsInterned() method to check whether or not a particular string has been interned earlier in the program’s execution.</p>
