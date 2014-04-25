---
layout: post
title: ! '[C#] Static type constructors and beforefieldinit'
category: [C#]
tags: [c#, clr, performance]
comments: true
share: true
---
If you’re a professional programmer (and chances are that you are), I’ll bet that at one time or another you’ve gotten into some form of quarrel over which of the following is “better”.

{% highlight csharp %}
class MessageHolder1
{
	static string Message = "Hello World!";
}
{% endhighlight %}

{% highlight csharp %}
class MessageHolder2
{
	static string Message;

	static MessageHolder2()
	{
		Message = "Hello World!";
	}
}
{% endhighlight %}

While I find the MessageHolder1 syntax to be stylistically superior, is it also in fact a potentially better choice when it comes to performance. The key to understanding why, as is frequently the case, is in the IL. Take a look at the definitions of the two classes.

{% highlight text %}
.class private auto ansi beforefieldinit ConsoleApplication1.MessageHolder1
       extends [mscorlib]System.Object
{
} // end of class ConsoleApplication1.MessageHolder1
{% endhighlight %}

{% highlight text %}
.class private auto ansi ConsoleApplication1.MessageHolder2
       extends [mscorlib]System.Object
{
} // end of class ConsoleApplication1.MessageHolder2
{% endhighlight %}

Notice anything? The definition for the MessageHolder1 class contains an additional type attribute, “beforefieldinit”. To understand what impact this attribute has, we must first get one thing out of the way. As far as C# is concerned, both class definitions are equivalent. Each class has what is known as a “type initializer”, which is simply code that is executed to prepare the type for use. MessageHolder1’s type initializer is defined implicitly in the static “message” variable assignment, and MessageHolder2’s type initializer is defined explicitly as a static type constructor.

What the beforefieldinit attribute does is define under what conditions the CLR can and must execute the class’s type initializer. When beforefieldinit is **not** present, a type’s initializer method is triggered by any of the following events:

<ol>
<li>First access to any static field of that type</li>
<li>First invocation of any static method of that type</li>
<li>First invocation of any instance or virtual method of that type if it is a value type</li>
<li>First invocation of any constructor for that type.</li>
</ol>
What this basically means is that before the type is used in any way, the type initializer method must be executed. It therefore makes good sense that this attribute is not applied to classes which contain an explicit type constructor. As the CLR doesn’t know what impact the type constructor may have on the class, or on any other component of the program for that matter, it must execute it right before the class is used in any way. This means that the type constructor’s execution is both reliable and predictable.

However, if the only job of a type initializer is to assign values to static variables, then we don’t necessarily need to be as demanding as to when the assignment takes place. All we require from the CLR is that the variable is assigned before we access it for the first time. And indeed, this is the impact that beforefieldinit has on a class. To quote the CIL spec, beforefieldinit ensures that…

> …the type’s initializer method is executed at, or sometime before, first access to any static field defined for that type.

This gives the CLR significantly more freedom as to when it wishes to assign static fields. Although we cannot be 100% sure of when the assignment will take place, the assumption here is that the CLR will act when it feels the time is right, which should in theory improve application efficiency.