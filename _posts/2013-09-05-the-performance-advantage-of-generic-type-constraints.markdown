---
layout: post
status: publish
published: true
title: The performance advantage of generic type constraints
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "Have a look at the following two methods.\r\n\r\n[csharp]\r\nstatic int
  CompareToSix(IComparable&lt;int&gt; input)\r\n{\r\n    return input.CompareTo(6);\r\n}\r\n\r\nstatic
  int CompareToSixGeneric&lt;T&gt;(T input)\r\n    where T : IComparable&lt;int&gt;\r\n{\r\n
  \   return input.CompareTo(6);\r\n}\r\n[/csharp]\r\n\r\nFunctionally they aren’t
  much different. Both take an IComparable<int>, compare it to the number six, and
  then return the result. However when I execute the following code which calls the
  two methods 100,000,000 times each and compares the running time:\r\n\r\n[csharp]\r\nstatic
  void Main(string[] args)\r\n{\r\n    var sw = new Stopwatch();\r\n\r\n    sw.Start();\r\n\r\n
  \   for (var i = 0; i &lt; 100000000; i++)\r\n        CompareToSix(2);\r\n\r\n    sw.Stop();\r\n
  \   Console.WriteLine(&quot;Non-Generic: &quot; + sw.ElapsedMilliseconds + &quot;ms&quot;);\r\n
  \   sw.Restart();\r\n\r\n    for (var i = 0; i &lt; 100000000; i++)\r\n        CompareToSixGeneric(2);\r\n\r\n
  \   sw.Stop();\r\n    Console.WriteLine(&quot;Generic: &quot; + sw.ElapsedMilliseconds
  + &quot;ms&quot;);\r\n    Console.ReadLine();\r\n}\r\n[/csharp]\r\n\r\nI get the
  following result.*\r\n\r\n\r\n<blockquote>Non-Generic: 1214ms\r\nGeneric: 512ms</blockquote>\r\n\r\n\r\nWeird,
  eh?\r\n"
wordpress_id: 2292
wordpress_url: http://www.levibotelho.com/?p=2292
date: !binary |-
  MjAxMy0wOS0wNSAxMzoyOTo0NSArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wOS0wNSAxMToyOTo0NSArMDIwMA==
categories:
- C#
- CLR
tags:
- c#
- clr
- performance
- generics
comments: []
---
<p>Have a look at the following two methods.</p>
<p>[csharp]<br />
static int CompareToSix(IComparable&lt;int&gt; input)<br />
{<br />
    return input.CompareTo(6);<br />
}</p>
<p>static int CompareToSixGeneric&lt;T&gt;(T input)<br />
    where T : IComparable&lt;int&gt;<br />
{<br />
    return input.CompareTo(6);<br />
}<br />
[/csharp]</p>
<p>Functionally they aren’t much different. Both take an IComparable<int>, compare it to the number six, and then return the result. However when I execute the following code which calls the two methods 100,000,000 times each and compares the running time:</p>
<p>[csharp]<br />
static void Main(string[] args)<br />
{<br />
    var sw = new Stopwatch();</p>
<p>    sw.Start();</p>
<p>    for (var i = 0; i &lt; 100000000; i++)<br />
        CompareToSix(2);</p>
<p>    sw.Stop();<br />
    Console.WriteLine(&quot;Non-Generic: &quot; + sw.ElapsedMilliseconds + &quot;ms&quot;);<br />
    sw.Restart();</p>
<p>    for (var i = 0; i &lt; 100000000; i++)<br />
        CompareToSixGeneric(2);</p>
<p>    sw.Stop();<br />
    Console.WriteLine(&quot;Generic: &quot; + sw.ElapsedMilliseconds + &quot;ms&quot;);<br />
    Console.ReadLine();<br />
}<br />
[/csharp]</p>
<p>I get the following result.*</p>
<blockquote><p>Non-Generic: 1214ms<br />
Generic: 512ms</p></blockquote>
<p>Weird, eh?<br />
<a id="more"></a><a id="more-2292"></a><br />
The reason why this occurs is due to the way in which the Compare method is called. The difference really boils down to the fact that</p>
<p>[csharp]<br />
int comparable = 2;<br />
comparable.CompareTo(6);<br />
[/csharp]</p>
<p>which is functionally similar to the CompareToSix method is not the same as</p>
<p>[csharp]<br />
IComparable&lt;int&gt; comparable = 2;<br />
comparable.CompareTo(6);<br />
[/csharp]</p>
<p>which is functionally similar to the CompareSixGeneric method.</p>
<p>The difference between the two is that when we cast a value type to an interface implementation, a boxing operation is necessary. However when we simply execute an interface method on a value type, no boxing is required. CompareToSix which takes an IComparable<int> invokes this boxing operation, whereas CompareToSixGeneric which takes a T : IComparable<int> does not. As proof, we can take a look at the IL for the below code which calls both methods.</p>
<p>[csharp]<br />
static void Main(string[] args)<br />
{<br />
    CompareToSix(2);<br />
    CompareToSixGeneric(2);<br />
}<br />
[/csharp]</p>
<p>And the IL...</p>
<p>[sourcecode]<br />
.method private hidebysig static void  Main(string[] args) cil managed<br />
{<br />
  .entrypoint<br />
  // Code size       20 (0x14)<br />
  .maxstack  8<br />
  IL_0000:  ldc.i4.2<br />
  IL_0001:  box        [mscorlib]System.Int32 // Boxing here...<br />
  IL_0006:  call       int32 ConsoleApplication9.Program::CompareToSix(class [mscorlib]System.IComparable`1&lt;int32&gt;)<br />
  IL_000b:  pop<br />
  IL_000c:  ldc.i4.2<br />
  // ...but not here!<br />
  IL_000d:  call       int32 ConsoleApplication9.Program::CompareToSixGeneric&lt;int32&gt;(!!0)<br />
  IL_0012:  pop<br />
  IL_0013:  ret<br />
} // end of method Program::Main<br />
[/sourcecode]</p>
<p>Note that as boxing only impacts value types, we would not observe such a performance difference if we passed a reference type to either method. It is also important to keep in mind that in my example above there was a performance hit of approximately 700ms when looping through the operation 100,000,000 times. The real performance impact of this difference in the vast majority of applications will therefore be insignificant. I'll even go one step further and state that 99% of the time you will be better off choosing the method which is the most readable and makes the most sense in the context of your solution, rather than the method which is better performing. All that aside however, you still may come across a situation where application performance is critical, and in such a scenario it is important to take heed to the impact that unnecessary boxing such as that found here can have on an application.</p>
<hr />
<em>* Although it should be obvious, I'll go ahead and state that these are the results that I obtained on my machine and that while you should in principle see the same behaviour on your machine, YMMV.</em></p>
