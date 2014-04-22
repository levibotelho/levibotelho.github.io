---
layout: post
status: publish
published: true
title: ! '[C#] How method calling works'
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "Method calling is a joint operation performed by the C# compiler and the
  CLR. As we will see in this article, the role of each can vary depending on the
  context in which the method is called.\n\n<h2>The type object</h2>\n\nThe key to
  understanding the basics of method calling in C# is understanding how the CLR manages
  types. For every type used in a program, the CLR maintains a corresponding type
  object on the managed heap which includes pretty much everything the runtime needs
  to know in regards to a given type. One of the things that the type object contains
  is a method table, which the runtime can query to determine which methods are implemented
  by a given type and where in the assembly the method implementation code can be
  found. The method table is indispensable when making virtual method calls, and as
  such the runtime must be able to access it quickly and easily. To make this possible,
  every object present on the managed heap contains what is known as a type object
  pointer, which provides the runtime with direct access to the type object for a
  given type.\n\n<h2>Polymorphism and virtual methods</h2>\n\nAlthough it may seem
  strange to have begun this article looking at virtual method calls instead of nonvirtual
  calls, most method calls in a given C# program are made virtually, including many
  made to nonvirtual methods. <a href=\"http://www.levibotelho.com/call-and-callvirt-in-cil/\"
  title=\"Call and callvirt in CIL\">We discussed this in depth in a recent article
  which looked at the difference between the call and callvirt CIL instructions</a>.\n\nNow
  then, down to business. In order to understand how the C# compiler and the CLR handle
  virtual method calls, we’ll use the following program as an example. \n[csharp]\r\nstatic
  void Main(string[] args)\r\n{\r\n    Console.WriteLine(GetString(&quot;Hello World&quot;));\r\n}\r\n\r\nstring
  GetString(object arg)\r\n{\r\n    return arg.ToString();\r\n}\r\n[/csharp]\n"
wordpress_id: 3042
wordpress_url: http://www.levibotelho.com/?p=3042
date: !binary |-
  MjAxMy0xMi0xNSAxNjowMjo1NCArMDEwMA==
date_gmt: !binary |-
  MjAxMy0xMi0xNSAxNTowMjo1NCArMDEwMA==
categories:
- C#
- CLR
tags:
- c#
- clr
comments: []
---
<p>Method calling is a joint operation performed by the C# compiler and the CLR. As we will see in this article, the role of each can vary depending on the context in which the method is called.</p>
<h2>The type object</h2>
<p>The key to understanding the basics of method calling in C# is understanding how the CLR manages types. For every type used in a program, the CLR maintains a corresponding type object on the managed heap which includes pretty much everything the runtime needs to know in regards to a given type. One of the things that the type object contains is a method table, which the runtime can query to determine which methods are implemented by a given type and where in the assembly the method implementation code can be found. The method table is indispensable when making virtual method calls, and as such the runtime must be able to access it quickly and easily. To make this possible, every object present on the managed heap contains what is known as a type object pointer, which provides the runtime with direct access to the type object for a given type.</p>
<h2>Polymorphism and virtual methods</h2>
<p>Although it may seem strange to have begun this article looking at virtual method calls instead of nonvirtual calls, most method calls in a given C# program are made virtually, including many made to nonvirtual methods. <a href="http://www.levibotelho.com/call-and-callvirt-in-cil/" title="Call and callvirt in CIL">We discussed this in depth in a recent article which looked at the difference between the call and callvirt CIL instructions</a>.</p>
<p>Now then, down to business. In order to understand how the C# compiler and the CLR handle virtual method calls, we’ll use the following program as an example.<br />
[csharp]<br />
static void Main(string[] args)<br />
{<br />
    Console.WriteLine(GetString(&quot;Hello World&quot;));<br />
}</p>
<p>string GetString(object arg)<br />
{<br />
    return arg.ToString();<br />
}<br />
[/csharp]<br />
<a id="more"></a><a id="more-3042"></a><br />
This is a classic case of polymorphism. Although within the context of GetString() ToString() is being called on an object, the implementation of ToString() that is called is that defined by System.String. But how does this work? The first step in the process is performed by the C# compiler. If we were to look at the code for this program, we would see that arg.ToString() is called with a “callvirt” instruction. This instructs the CLR to call ToString() virtually.</p>
<p>What actually happens is that when ToString() is called, the runtime accesses arg’s type object via its type object pointer. Because arg is really a string, the type object for System.String will be accessed. The runtime will then access the type object’s method table and see that it System.String provides an implementation for ToString(). It will then access the method code, compile it if necessary, and execute it.</p>
<p>On the other hand, if we passed an object to GetString() that did not provide an implementation of ToString(), the CLR would simply begin walking up the inheritance tree, checking each ancestor type until it found one which did. This is made possible because each type object contains a reference to its parent type. An inheritance chain is therefore created for every type in a .NET program which in every case leads back to System.Object.</p>
<h2>Value types</h2>
<p>Although brilliantly designed, the above algorithm for making virtual method calls has one important weakness. It is entirely dependent on the type object, and therefore the type object pointer. So then what happens if we call a method on a value type which is not necessarily represented on the managed heap, and therefore may not contain a type object pointer? Well, as is often the case the best way to find out is by writing a small test program.</p>
<p>[csharp]<br />
static void Main(string[] args)<br />
{<br />
    var sb = new StringBuilder(&quot;Hello world.&quot;);<br />
    sb.ToString();<br />
    6.ToString();<br />
    GetString(sb);<br />
    GetString(6);<br />
}</p>
<p>static string GetString(object arg)<br />
{<br />
    return arg.ToString();<br />
}</p>
<p>static string GetString(int arg)<br />
{<br />
    return arg.ToString();<br />
}<br />
[/csharp]</p>
<p>Let’s start by looking at the IL for the two calls to ToString().</p>
<p>[sourcecode]<br />
// StringBuilder<br />
IL_000c:  callvirt   instance string [mscorlib]System.Object::ToString()<br />
// ...<br />
// Int32<br />
IL_001a:  call       instance string [mscorlib]System.Int32::ToString()<br />
[/sourcecode]</p>
<p>When ToString() is called on the StringBuilder instance, we can see that on the IL level, we are calling ToString() polymorphically on System.Object. Because StringBuilder is a reference type, the runtime will use the type object to navigate to the correct implementation of ToString(), and the code will function as anticipated. In this case, the C# compiler hasn’t done a whole lot and has left it up to the CLR to determine which method to invoke.</p>
<p>This changes considerably, however, when we call ToString() on System.Int32, a value type which does not have an integrated a type object pointer. In this case, the C# compiler has instructed the CLR to make a nonvirtual call to the implementation of ToString() that is written directly into System.Int32. The CLR therefore doesn’t need to access the type object. It simply follows the instructions that the C# compiler has given it.</p>
<p>Now let’s look at what happens when we call GetString(), which takes an object as a parameter and returns the result of calling ToString() on the object. The first thing we need to do is to look at the IL for the GetString method.</p>
<p>[sourcecode]<br />
.method private hidebysig static string  GetString(object arg) cil managed<br />
{<br />
  // Code size       7 (0x7)<br />
  .maxstack  8<br />
  IL_0000:  ldarg.0<br />
  IL_0001:  callvirt   instance string [mscorlib]System.Object::ToString()<br />
  IL_0006:  ret<br />
} // end of method Program::GetString<br />
[/sourcecode]</p>
<p>As the C# compiler cannot know ahead of time on what type ToString() will really be called, it simply instructs the CLR to make a virtual method call to ToString() and lets the CLR determine which implementation to invoke. This is exactly the same code that we saw when we called ToString() directly on our instance of StringBuilder earlier on. It should therefore come as no surprise that when we pass a StringBuilder to GetString, the StringBuilder is simply loaded onto the stack and the GetString() method is invoked like so.</p>
<p>[sourcecode]<br />
  IL_0024:  ldloc.0<br />
  IL_0025:  call       string ConsoleApplication1.Program::GetString(object)<br />
[/sourcecode]</p>
<p>As our instance of StringBuilder lives on the heap, when executing GetString() the CLR use the StringBuilder’s type object pointer to access its type object’s method tables and will invoke the correct implementation of ToString().</p>
<p>But what will happen when we pass an Int32 to GetString()?</p>
<p>[sourcecode]<br />
  IL_002f:  ldc.i4.6<br />
  IL_0030:  box        [mscorlib]System.Int32<br />
  IL_0035:  call       string ConsoleApplication1.Program::GetString(object)<br />
[/sourcecode]</p>
<p>Aha! In order to make this call, the compiler has gone ahead and boxed our Int32 before passing it as an argument to GetString(). During the boxing, a type object pointer will have been created and embedded in the object’s implementation on the heap. When ToString() is invoked on our Int32 in the context of GetString(), the CLR will be able to use this type object pointer to find the correct implementation of ToString() to execute</p>
<h2>A quick summary…</h2>
<p>Hopefully this article will have provided you with some insight into how method calls are handled in C#. To sum up, here are the main take-away points:</p>
<ul>
<li>Method calls are handled jointly by the C# compiler and the CLR</li>
<li>Virtual method calls, which are the most common type of method call in C#, are made possible primarily by the CLR and the type object corresponding to the variable on which the method is being called.</li>
<li>Nonvirtual method calls are handled in large part by the C# compiler, and require considerably less CLR magic behind the scenes.</li>
</ul>
<h2>But what about the boxing?!</h2>
<p>If you’re a regular reader of this blog, you’ll know that I love writing about how to avoid unnecessary boxing operations. I couldn’t leave this post without taking a final moment to look at how to avoid the type of boxing that we saw when we passed our Int32 to our GetString(object) method. The truth is that avoiding the boxing is incredibly simple. You simply need to provide a method overload for any value types that you reasonably expect to pass to the method. In fact, you’ll see this trick used fairly frequently in the .NET Framework. Console.WriteLine() is a good example. In the case of passing an Int32 to our GetString method, we would simply need to provide an overload such as this.</p>
<p>[csharp]<br />
static string GetString(int arg)<br />
{<br />
    return arg.ToString();<br />
}<br />
[/csharp]</p>
<p>If we were to look at the IL for this method, we would see that ToString would be called on System.Int32 and not on System.Object. This assertion is made by the compiler, and is logically valid for two reasons:</p>
<ol>
<li>We cannot pass a less-derived type (i.e. an object) to GetString(int).</li>
<li>We cannot pass a type derived from Int32 to this method, because value types do not support inheritance. </li>
</ol>
<p>As the only type of parameter that can be passed to this method is an Int32, the compiler can therefore instruct the CLR to call Int32’s implementation of ToString() with no ill effects.</p>
