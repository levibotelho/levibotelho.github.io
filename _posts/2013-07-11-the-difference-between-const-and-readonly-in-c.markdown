---
layout: post
title: The difference between const and readonly in C#
category: Development
tags: [c#, clr]
description: The const and readonly keywords are similar to one another, but not knowing how they differ can lead to particularly nasty bugs in certain circumstances. Learn about the difference between the two and when to use one over the other. 
comments: true
share: true
redirect_from: "/the-difference-between-const-and-readonly-in-c/"
---
Const and readonly are two very useful keywords in C#. While they both perform roughly the same function, they are not implemented in the same way. As we’ll see later, being aware of the real difference between the two can help us write more robust applications.

In short, the difference between const and readonly is that a const’s value is known at compile time, and that a readonly variable’s value does not need to be known until runtime. Or, to be more specific, a readonly variable’s value does not need to be known until the constructor of the class containing the readonly variable finishes executing.

So that’s the short answer to the question. However it isn’t the full story.<a id="more"></a><a id="more-1882"></a> Take a look at the following code:

{% highlight csharp %}
const MyClass myClass = new MyClass();
{% endhighlight %}

Where MyClass is defined as:

{% highlight csharp %}
class MyClass
{
    const int MyInt = 6;
}
{% endhighlight %}

The fact that this doesn't compile seems quite strange at first. If a const is simply a value that needs to be known at compile time, then nothing should prevent us from doing this. The truth, however, is that a const is more than just a known value. To understand what a const really is requires that we look at some IL. Take the following program:

{% highlight csharp %}
class Program
{
    const string Greeting = "Hello World";

    static void Main(string[] args)
    {
        Console.WriteLine(Greeting);
    }
}
{% endhighlight %}

The corresponding IL for the Main method is as follows:

{% highlight text %}
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       11 (0xb)
  .maxstack  8
  IL_0000:  ldstr      "Hello World"
  IL_0005:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_000a:  ret
} // end of method Program::Main
{% endhighlight %}

Now look at what happens if we change the const value to be static readonly:

{% highlight text %}
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       11 (0xb)
  .maxstack  8
  IL_0000:  ldsfld     string ConsoleApplication2.Program::Greeting
  IL_0005:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_000a:  ret
} // end of method Program::Main
{% endhighlight %}

Notice the instruction IL_0000 in both cases. This is the key to understanding the difference between const and readonly. *When we use const to declare a constant value, the value is embedded directly into our code. When we use a readonly variable, the variable itself is referenced.*

So, returning to our first example, the reason why we cannot declare

{% highlight csharp %}
const MyClass myClass = new MyClass();
{% endhighlight %}

is because myClass holds the memory address of an instantiation of a MyClass object. Because we cannot know this address at compile time, we cannot embed this value into our IL as is necessary when declaring a constant.

This distinction between consts and readonlys becomes very important when we start referencing these values across assemblies. For example, imagine that we have two assemblies: Assembly A and Assembly B. Assembly A contains the following code:

{% highlight csharp %}
namespace AssemblyA
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine(AssemblyB.ContainerClass.Value);
        }
    }
}
{% endhighlight %}

And Assembly B contains this:

{% highlight csharp %}
namespace AssemblyB
{
    public class ContainerClass
    {
        public const int Value = 6;
    }
}
{% endhighlight %}

If we deploy these assemblies together and run the program, the number 6 will be written to the console. However, if we modify the Value const in Assembly B and redeploy it without rebuilding Assembly A, *the program will continue to write number 6 to the console*. While this particular example is trivial, you can imagine the consequences that this kind of error can have on a real-world application. For this reason, unchanging public and protected values should only be declared as constants if they are sure to never change. If there is even a slight risk of them changing in the future, they should be declared as public static readonly variables, which will result in them being referenced by calling code instead of them being embedded directly in it.

