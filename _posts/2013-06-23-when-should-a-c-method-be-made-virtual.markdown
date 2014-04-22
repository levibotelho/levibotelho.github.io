---
layout: post
status: publish
published: true
title: When should a C# method be made virtual?
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "<a href=\"http://www.levibotelho.com/polymorphism-with-new-and-override/\"
  title=\"Polymorphism with new and override\" target=\"_blank\">In the last post</a>
  we saw how the behaviour of base and derived classes changes with the presence of
  the new or override keyword on inherited methods. In this post we’re going to see
  the impact that this has on class design. Let’s start with some code:\r\n[csharp]\r\nstatic
  void Main(string[] args)\r\n{\r\n    var input = new DerivedClass();\r\n    WriteClassName(input);
  // Outputs BaseClass\r\n    Console.ReadLine();\r\n}\r\n\r\nstatic void WriteClassName(BaseClass
  input)\r\n{\r\n    input.WriteName();\r\n}\r\n\r\nclass BaseClass\r\n{\r\n    public
  void WriteName()\r\n    {\r\n        Console.WriteLine(GetName());\r\n    }\r\n\r\n
  \   protected string GetName()\r\n    {\r\n        return &quot;BaseClass&quot;;\r\n
  \   }\r\n}\r\n\r\nclass DerivedClass : BaseClass\r\n{\r\n    protected new string
  GetName()\r\n    {\r\n        return &quot;DerivedClass&quot;;\r\n    }\r\n}\r\n[/csharp]\r\nThis
  is a lot like the code we saw last week. The main difference is that here the exposed
  method is “WriteName”, which calls the class’s “GetName” method to get the class
  name. When run, this program outputs \"BaseClass\", which is to be expected given
  what we learned last time around."
wordpress_id: 1632
wordpress_url: http://www.levibotelho.com/?p=1632
date: !binary |-
  MjAxMy0wNi0yMyAxMDoyNzo1MyArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNi0yMyAwODoyNzo1MyArMDIwMA==
categories:
- C#
tags:
- c#
- polymorphism
comments: []
---
<p><a href="http://www.levibotelho.com/polymorphism-with-new-and-override/" title="Polymorphism with new and override" target="_blank">In the last post</a> we saw how the behaviour of base and derived classes changes with the presence of the new or override keyword on inherited methods. In this post we’re going to see the impact that this has on class design. Let’s start with some code:<br />
[csharp]<br />
static void Main(string[] args)<br />
{<br />
    var input = new DerivedClass();<br />
    WriteClassName(input); // Outputs BaseClass<br />
    Console.ReadLine();<br />
}</p>
<p>static void WriteClassName(BaseClass input)<br />
{<br />
    input.WriteName();<br />
}</p>
<p>class BaseClass<br />
{<br />
    public void WriteName()<br />
    {<br />
        Console.WriteLine(GetName());<br />
    }</p>
<p>    protected string GetName()<br />
    {<br />
        return &quot;BaseClass&quot;;<br />
    }<br />
}</p>
<p>class DerivedClass : BaseClass<br />
{<br />
    protected new string GetName()<br />
    {<br />
        return &quot;DerivedClass&quot;;<br />
    }<br />
}<br />
[/csharp]<br />
This is a lot like the code we saw last week. The main difference is that here the exposed method is “WriteName”, which calls the class’s “GetName” method to get the class name. When run, this program outputs "BaseClass", which is to be expected given what we learned last time around.<a id="more"></a><a id="more-1632"></a> Similarly, if we modify our classes as such:<br />
[csharp]<br />
class BaseClass<br />
{<br />
    public void WriteName()<br />
    {<br />
        Console.WriteLine(GetName());<br />
    }</p>
<p>    protected virtual string GetName()<br />
    {<br />
        return &quot;BaseClass&quot;;<br />
    }<br />
}</p>
<p>class DerivedClass : BaseClass<br />
{<br />
    protected override string GetName()<br />
    {<br />
        return &quot;DerivedClass&quot;;<br />
    }<br />
}<br />
[/csharp]<br />
It should be equally unsurprising that this outputs “DerivedClass”.</p>
<p>But let’s take a moment to think about what has happened here. In both cases, BaseClass.WriteName() is called, which then goes on to call the GetName() method. However while BaseClass.GetName() is called when GetName() is nonvirtual, DerivedClass.GetName() is called when it is. What this shows us is that <em>making a method virtual allows the functionality of the base class to be altered in a derivation of the class.</em></p>
<p>The impact of this is far-reaching and shouldn't be underestimated. While it can be very useful to override base class logic (think Object.ToString()), in many cases base classes encapsulate functionality that shouldn’t be modifiable. A good example of this is ClientBase<>, which serves as the base class for WCF client classes. While ClientBase<> is designed to be inherited in order to generate clients for various WCF services, derived classes should not be able to impact the core logic required to construct (or teardown) a WCF client altogether. It is the responsibility of the developer of a class to ensure that a it remains functionally robust when it is used in the future. The developer writing an inheriting class should only be concerned about correctly implementing their own functionality, and should not have to concern themselves with potentially breaking the base class when they implement their own methods.*</p>
<p>So, while virtual methods are a key ingredient in extensible class design, one has to keep in mind that they open base class functionality up to modification**. If this is not explicitly desired, then a method should not be made virtual. Non-virtual methods can still be hidden by use of the new keyword, which exists specifically to allow a method to be replaced in a derived class, while keeping the base class functionality intact.</p>
<hr />
* <em>This is the exact same concept as that which applies to access modifiers.</em></p>
<p>** <em>Virtual methods also tend to slightly underperform nonvirtual methods for several reasons which are beyond the scope of this post. While this performance impact is trivial in many cases, in high-performance scenarios it may be worthy of consideration.</em></p>
