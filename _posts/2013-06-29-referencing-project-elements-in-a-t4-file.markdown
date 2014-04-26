---
layout: post
title: Referencing project elements in a T4 file
category: C#
tags: [c#, t4, metaprogramming]
comments: true
share: true
---
*Inspired by [this Stack Overflow question](http://stackoverflow.com/q/17123991/1068266). Give the asker an upvote if you have a minute.*

<hr />
It often occurs when writing a T4 template that you need to reference some element of the project containing the template. However, simply trying to reference a variable or call a function can be very frustrating. Especially because the current lack of T4 IntelliSense requires you to regenerate your code over and over again, only to find out that the “type or namespace name could not be found”.

The reason why this is so frustrating is because of a natural mistake people make, which is thinking that a T4 file is just like any other code file in your project.<a id="more"></a><a id="more-1552"></a> This isn’t true. A better way to think of a .tt is as an independent set of instructions used to drop a generated file into your project when it is run. Because the .tt is independent, <#= ConsoleApplication1.MyClass.MyVariable #> is meaningless without a reference to the assembly containing *ConsoleApplication1.MyClass.MyVariable*. To create this reference, add the following include statement to your T4:

{% highlight csharp %}
<#@ assembly name="$(TargetPath)" #>
{% endhighlight %}

Don’t forget the namespace:

{% highlight csharp %}
<#@ import namespace="ConsoleApplication2" #>
{% endhighlight %}

Now you’ll be able to reference public elements of your project. But there is one more “gotcha”. Because your .tt is referencing your project assembly, if your assembly is out of date or simply hasn’t been built yet, your T4 won’t generate correctly. This is especially frustrating when your generated file has compilation errors, because you’ll end up in a deadlock when your noncompiling generated file is preventing you from correcting the errors contained within it. The solution to this is to manually make just enough corrections in the generated file to eliminate all compile time errors and then rebuild your project. You can then regenerate your file which will hopefully be now free of errors and then rebuild the project once more to take into account the generated code.

