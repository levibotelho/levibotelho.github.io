---
layout: post
title: What is string interning?
category: Development
tags: [c#, clr]
comments: true
share: true
---
Execute this block of code in a new console application:

{% highlight csharp %}
var array1 = new[] { 10 };
var array2 = new[] { 10 };
Console.WriteLine(ReferenceEquals(array1, array2));
Console.ReadLine();
{% endhighlight %}

Most people would expect it to print “False”, which is exactly what it does. All that you’ve done is create two new integer arrays and then tested to see if their references on the stack are the same, which they logically are not.

Now execute this block of code:

{% highlight csharp %}
var string1 = "Hello world";
var string2 = "Hello world";
Console.WriteLine(ReferenceEquals(string1, string2));
Console.ReadLine();
{% endhighlight %}

There is a distinct possibility (especially If you are running .NET 4.5) that it will print “true”. What’s going on?
<a id="more"></a><a id="more-532"></a>
This is the result of string interning. String interning is a process whereby a single copy of a given string is stored in application memory, and referenced everywhere where a new copy of the same string would otherwise be created. The most obvious advantage to doing this is that it prevents the same string from being stored in memory multiple times, however it also provides a performance boost by making string comparisons significantly easier. Two variables holding the same interned string will forcibly contain the same reference.

## How does it work?

<span style="line-height: 1.6em;">Interning in .NET is managed with the help of a hashtable. When the CLR wants to intern a string, it hashes it and checks the resulting value against the hashtable to see if the string has already been interned. If is has, the string variable's reference is updated to point to the interned version of the string. If it hasn't yet been interned, it creates a new entry in the intern table and the reference is updated accordingly.</span>

## When are strings interned?

The answer isn’t exactly simple. The CLR will by default intern all string literals found in your application, but the C# compiler overrides this behaviour by default because this interning results in a performance hit when the application is started. Even then however, the CLR can still choose to intern.

The only bulletproof way to ensure that a given string is interned is by interning it yourself, with the string.Intern() method. You can also use the string.IsInterned() method to check whether or not a particular string has been interned earlier in the program’s execution.

