---
layout: post
status: publish
published: true
title: Polymorphism with new and override
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "What would you expect the console output to be of the following program?\r\n\r\n[csharp]\r\nstatic
  void Main(string[] args)\r\n{\r\n    var input = new DerivedClass();\r\n    PrintName(input);\r\n
  \   Console.ReadLine();\r\n}\r\n\r\nstatic void PrintName(BaseClass input)\r\n{\r\n
  \   Console.WriteLine(input.GetName());\r\n}\r\n\r\nclass BaseClass\r\n{\r\n    public
  virtual string GetName()\r\n    {\r\n        return &quot;BaseClass&quot;;\r\n    }\r\n}\r\n\r\nclass
  DerivedClass : BaseClass\r\n{\r\n    public override string GetName()\r\n    {\r\n
  \       return &quot;DerivedClass&quot;;\r\n    }\r\n}\r\n[/csharp]\r\n\r\nWell,
  the answer is:\r\n<blockquote>DerivedClass</blockquote>\r\nThis is simply standard
  C# behaviour. However the reason <em>why</em> this happens is quite interesting.\r\n"
wordpress_id: 1482
wordpress_url: http://www.levibotelho.com/?p=1482
date: !binary |-
  MjAxMy0wNi0xNSAxMToxODoxNyArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNi0xNSAwOToxODoxNyArMDIwMA==
categories:
- C#
- CLR
tags:
- c#
- clr
- polymorphism
comments: []
---
<p>What would you expect the console output to be of the following program?</p>
<p>[csharp]<br />
static void Main(string[] args)<br />
{<br />
    var input = new DerivedClass();<br />
    PrintName(input);<br />
    Console.ReadLine();<br />
}</p>
<p>static void PrintName(BaseClass input)<br />
{<br />
    Console.WriteLine(input.GetName());<br />
}</p>
<p>class BaseClass<br />
{<br />
    public virtual string GetName()<br />
    {<br />
        return &quot;BaseClass&quot;;<br />
    }<br />
}</p>
<p>class DerivedClass : BaseClass<br />
{<br />
    public override string GetName()<br />
    {<br />
        return &quot;DerivedClass&quot;;<br />
    }<br />
}<br />
[/csharp]</p>
<p>Well, the answer is:</p>
<blockquote><p>DerivedClass</p></blockquote>
<p>This is simply standard C# behaviour. However the reason <em>why</em> this happens is quite interesting.<br />
<a id="more"></a><a id="more-1482"></a><br />
When we think of instantiating an object on the heap in C#, we often imagine a single block of memory carved out for the object, containing all the necessary data required to make the object function as it is supposed to. While this makes for easy conceptualisation, it is actually somewhat inaccurate.</p>
<p>In reality, an instantiated object is made up of several components. The first component is an object which contains data specific to that instantiation of the class. Think fields. This includes fields defined in parent classes. For simplicity’s sake I’ll refer to this object as an “instance object” from here on in.</p>
<p>This instance object points to a second object on the heap: a type object. This is literally an instance of System.Type, and is what is served to you when you execute .GetType() on an object in your code. The type object contains type-specific data, such as static fields, as well as a table that lists all of the type’s methods and points to their individual implementations. It also contains a pointer to the type object for the type’s base type, which allows the CLR to walk up the inheritance tree if needed.</p>
<p>When you think about it, this architecture makes a lot of sense. Instance objects hold data on each specific instantiation of a given class and are linked to type objects which contain data applicable to all implementations of a given class.</p>
<p>Now, let’s get back to our problem. The reason why “DerivedClass” is outputted in our method is due to the algorithm used by the code to navigate to the implementation of virtual methods. In our case, the scope of the function defines that we are navigating to GetName() on what is either a BaseClass object or a BaseClass derivative. The CLR navigates to the implementation as follows:</p>
<ol>
<li>The code gets the memory addrerss of “input” from the stack and navigates to its implementation on the heap.</li>
<li>The code navigates to the type object of “input”, which in this case is the “DerivedClass” type object.</li>
<li>The code calls the “GetName()” method referenced in the method table in the type object. The JITter compiles this code if it hasn’t been already. The code is then executed, and “DerivedClass” is written to the console window.</li>
</ol>
<p>Now for something really interesting. Let’s modify our two classes:</p>
<p>[csharp]<br />
class BaseClass<br />
{<br />
    public string GetName()<br />
    {<br />
        return &quot;BaseClass&quot;;<br />
    }<br />
}</p>
<p>class DerivedClass : BaseClass<br />
{<br />
    public new string GetName()<br />
    {<br />
        return &quot;DerivedClass&quot;;<br />
    }<br />
}<br />
[/csharp]</p>
<p>Now, instead of overriding a virtual method we are hiding a nonvirtual method. When we rerun the same code as before, the console reads:</p>
<blockquote><p>BaseClass</p></blockquote>
<p>Why is this? Notice that when I described the method navigation algorithm, I specifically stated that it applied to virtual methods. When the method being called is nonvirtual, as is the case here, the algorithm used to find and execute the method is slightly different:</p>
<ol>
<li>The code gets the memory address of “input” from the stack and navigates to its implementation on the heap.</li>
<li>The code navigates to the type object of “input”, which in this case is the “DerivedClass” type object. This type is compared to the type of the object in the scope of the function in which it is called.</li>
<li>When the code discovers that the type of the backing input object (DerivedClass) is not the same as the type of the variable in the scope of the function (BaseClass), it internally constructs a new BaseClass object, which is linked to the BaseClass type object.</li>
<li>The code calls the “GetName()” method referenced in the method table in the BaseClass type object. The JITter compiles this code if it hasn’t been already and executes it, writing “BaseClass” to the console window.</li>
</ol>
<p><a href="http://www.levibotelho.com/when-should-a-c-method-be-made-virtual/" title="When should a C# method be made virtual" target="_blank">In the next post: A logical explanation for all this...</a></p>
