---
layout: post
title: call and callvirt in CIL
category: Development
tags: [c#, clr]
description: The call and callvirt CIL instructions dictate how method calling behaves in .NET applications. See how they work and how they impact your programs.
comments: true
share: true
redirect_from: "/call-and-callvirt-in-cil/"
---
If you’ve ever looked at even small amounts of CIL, you’ll notice that two different instructions are used to call methods: “call” and “callvirt”. My goal in this post is to introduce these two methods and provide a general understanding of how they are used.

## call – The basics

Call provides basic method calling functionality in CIL. Let’s jump right into an example to see how it works.

{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        Printer.Print("Hello World");
    }
}

public class Printer
{
    public static void Print(string message)
    {
        Console.WriteLine(message);
    }
}
{% endhighlight %}

&nbsp;

{% highlight text %}
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       11 (0xb)
  .maxstack  8
  IL_0000:  ldstr      "Hello World"
  IL_0005:  call       void ConsoleApplication1.Printer::Print(string)
  IL_000a:  ret
} // end of method Program::Main
{% endhighlight %}

&nbsp;

{% highlight text %}
.method public hidebysig static void  Print(string message) cil managed
{
  // Code size       7 (0x7)
  .maxstack  8
  IL_0000:  ldarg.0
  IL_0001:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_0006:  ret
} // end of method Printer::Print
{% endhighlight %}

There isn’t actually anything too complicated going on here. When we execute Main, “Hello World” is loaded onto the stack, and then the Print method, which takes a single string parameter, is called using the “call” instruction. Notice that the call instruction itself takes as a descriptor a reference to the method to call. (This reference is actually a metadata token, but going into details about metadata is a topic for another day.) When call executes, it pops the number of arguments of the stack that the method being called requires, and passes them as zero-indexed arguments to the method. We can see this in action at line IL_0000 of the Print method, where we load “argument 0” onto the stack so that it can be passed to the Console.WriteLine method by another “call” invocation. In our case the Print method doesn’t return anything, but if it did the return value would simply be pushed onto the stack before the final “ret” call of the method.

## callvirt – The basics

Perhaps the easiest way to distinguish call from callvirt is to refer to their different descriptions in the CIL spec. While call is simply used to “call a method”, “callvirt” is used to “call a method associated, at runtime, with an object”. To understand how the notion of an object impacts a method call, take this function.

{% highlight csharp %}
public static void Print(object thingy)
{
    Console.WriteLine(thingy.ToString());
}
{% endhighlight %}

[As we learned back in this post](http://www.levibotelho.com/polymorphism-with-new-and-override/), the behaviour that this method will exhibit is entirely dependent on what type “thingy” really is, due to the fact that ToString() is a virtual method. But how can the runtime know what implementation of “ToString” to call if it calls it on a simple object? Well, this is where callvirt really starts to make sense. Callvirt takes into account the type of the object on which the method is being called in order to provide us with the polymorphic behaviour that we expect from such cases. All that is required in order to execute a callvirt instruction is to pass a pointer to the object on which the method is being called. We can see this if we look at the IL of the ToString() call in the Print method.

{% highlight text %}
.method private hidebysig instance void  Print(object thingy) cil managed
{
  // Code size       12 (0xc)
  .maxstack  8
  IL_0000:  ldarg.1
  IL_0001:  callvirt   instance string [mscorlib]System.Object::ToString()
  IL_0006:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_000b:  ret
} // end of method Printer::Print
{% endhighlight %}

The override of ToString() that we are calling doesn’t take any parameters, however before calling it, argument at index 0 is loaded onto the stack. Argument at index 0 is of course “thingy”, whatever it happens to be. When callvirt is executed to call the ToString() method, it first verifies that “thingy” isn’t null, and then goes on to determine the type of “thingy” before locating the correct instance of ToString() to call by walking up the inheritance tree until it finds a valid ToString() implementation.

## When callvirt replaces call...

So far the distinction that we have made between call and callvirt has been simple: call provides simple method calling functionality, while callvirt provides support for virtual methods and polymorphism. However, if you begin to examine the IL of your own C# programs you’ll notice that callvirt is also used to call nonvirtual instance methods. But why would the C# compiler do this? Off the top of my head I can think of two advantages:

<ol>
<li>Nonvirtual methods can be made virtual without recompiling calling assemblies.</li>
<li>Developers don’t need to keep track of which methods are virtual and can therefore be called on null references (because of callvirt’s integrated null check). This “feature” is limiting, but simplifies coding.
It is also important to understand that calling nonvirtual methods with callvirt doesn’t impact performance as much as one may think. While the null reference check integrated into callvirt is still performed on nonvirtual calls, if the jitter knows that a given method is nonvirtual it won’t bother searching through the inheritance tree to find the correct method implementation. It’ll go straight to the correct implementation just as call would. This makes callvirt almost as fast as call when calling nonvirtual instance methods.</li>
</ol>
## When call replaces callvirt...

Despite the fact that it doesn’t have “virt” in the name, call can still be used to call nonvirtual methods. It simply calls them nonvirtually, invoking the method declared on the type of the variable instance as it appears in the calling scope. An example of when this occurs is when an overriding method calls a base implementation. Were the call to be made with callvirt, the runtime would end up re-calling the derived implementation which would then re-call the base implementation and so on and so forth until a stack overflow occurred.

## Final word

Hopefully by now you’ll have a decent understanding of the call and callvirt instructions. This understanding will be important in several upcoming articles, so stay tuned to make use of what we’ve discussed.