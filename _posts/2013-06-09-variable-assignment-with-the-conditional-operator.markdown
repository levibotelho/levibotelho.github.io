---
layout: post
status: publish
published: true
title: Variable assignment with the conditional operator
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "Take a look at this statement:\r\n\r\n[csharp]\r\nint? nullableInt = DateTime.Now.Second
  == 30 ? 55 : null;\r\n[/csharp]\r\n\r\nIf you haven’t tried this yourself at some
  point in the past, then you might not see anything wrong with it. The same goes
  with this statement:\r\n\r\n[csharp]\r\nobject aMysteriousObject = DateTime.Now.Second
  == 51 ? &quot;hello&quot; : 6;\r\n[/csharp]\r\n\r\nBut the truth is that neither
  of them compile. Why?\r\n"
wordpress_id: 1072
wordpress_url: http://www.levibotelho.com/?p=1072
date: !binary |-
  MjAxMy0wNi0wOSAyMzowNjoyMCArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNi0wOSAyMTowNjoyMCArMDIwMA==
categories:
- C#
tags:
- c#
comments: []
---
<p>Take a look at this statement:</p>
<p>[csharp]<br />
int? nullableInt = DateTime.Now.Second == 30 ? 55 : null;<br />
[/csharp]</p>
<p>If you haven’t tried this yourself at some point in the past, then you might not see anything wrong with it. The same goes with this statement:</p>
<p>[csharp]<br />
object aMysteriousObject = DateTime.Now.Second == 51 ? &quot;hello&quot; : 6;<br />
[/csharp]</p>
<p>But the truth is that neither of them compile. Why?<br />
<a id="more"></a><a id="more-1072"></a><br />
The reason is that the C# compiler verifies that the true and false operands in the statement are implicitly compatible with one another. In the first statement, 51 is taken to be an int. As null cannot be assigned to an integer, the compiler throws an error. The same logic goes for the second statement, as "hello" is determined to be a string and 6 an integer, even though both are also objects.</p>
<p>The way to fix these statements is to cast one of the values to a compatible type. Interestingly enough, both values do not have to be cast. Casting one of the two is enough to make the compiler see that the types are compatible and allow the statement. Therefore, the way to make them compile is to change them to one of the following:</p>
<p>[csharp]<br />
int? nullableInt = DateTime.Now.Second == 30 ? (int?)55 : null;<br />
int? nullableInt2 = DateTime.Now.Second == 30 ? 55 : (int?)null:<br />
[/csharp]</p>
<p>[csharp]<br />
object aMysteriousObject = DateTime.Now.Second == 51 ? (object)&quot;hello&quot; : 6;<br />
object aMysteriousObject2 = DateTime.Now.Second == 51 ? &quot;hello&quot; : (object)6;<br />
[/csharp]</p>
