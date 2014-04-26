---
layout: post
title: ! '[C#] How does the garbage collector work?'
category: C#
tags: [c#, clr]
comments: true
share: true
---
## What is garbage?

“Garbage” consists of objects created during a program’s execution on the managed heap that are no longer accessible by the program. Their memory can be reclaimed and reused with no averse effects.

## What is the garbage collector?

The garbage collector is a mechanism which identifies garbage on the managed heap and makes its memory available for reuse. This eliminates the need for the programmer to manually delete objects which are no longer required for program execution. This reuse of memory helps reduce the amount of total memory that a program needs to run. In technical terms, we say that it keeps the program’s “working set” small.

## How does the garbage collector identify garbage?

In Microsoft's implementation of the .NET framework the garbage collector determines if an object is garbage by examining the reference type variables pointing to it. In the context of the garbage collector, reference type variables are known as “roots”. Examples of roots include:

<ul>
<li>A reference on the stack</li>
<li>A reference in a static variable</li>
<li>A reference in another object on the managed heap that is not eligible for garbage collection</li>
<li>A reference in the form of a local variable in a method</li>
</ul>

Take the following method.

{% highlight csharp %}
void CreateList()
{
	var myList = new List<object>();
	myList.Add(new object());
	myList.Add(new object());
	myList.Add(new object());
	Console.WriteLine("Objects added!");
}
{% endhighlight %}

When the method starts to execute, a  `List<object>` is instantiated on the managed heap along with several objects. The List contains a root to each of the objects, and the stack contains a root to the List. While the method is executing, all of these roots are accessible from within the program and are considered to be “active”. When the method finishes executing, the stack is cleaned up, removing the root pointing to the List. The List is now no longer accessible within the program. All of the roots contained by the List (those pointing to the objects) are now considered to be “inactive”.

The garbage collector identifies garbage by examining an application’s roots. Objects which have no active roots pointing to them are considered to be garbage.
<a id="more"></a><a id="more-3232"></a>

## How does the garbage collector manage to collect garbage without impacting application performance?

The truth is that the garbage collector *does* impact application performance. However, Microsoft has done a very good job at ensuring that it runs as quickly and efficiently as possible, and its impact is virtually unnoticeable from a user standpoint. It manages to do this by employing a wide variety of strategies and optimisations, a few of which we’ll talk about here.

### Reference tracking optimisations

When the garbage collector begins a collection, it starts by setting a bit on every object which *could potentially* be garbage to 0. This marks these objects for collection. It then traverses the active roots in the application and sets that bit to 1 on every object which is not in fact garbage.

Remember from our previous example that when an object is considered not to be garbage, all objects that it references are also considered not to be garbage. This means that marking a single object as not garbage (such as a List) can result in hundreds or thousands of others also being marked. The garbage collector makes this process more efficient by examining the garbage collection bit before marking a given object. If the bit is already set to 1, it simply moves on, knowing that the object and its roots and their roots and so on and so forth have already been traversed. This makes the marking process significantly more efficient.

### Generations

The garbage collector makes one big assumption about the lifetime of objects in an application.

> The length of time that an object has been alive is inversely proportional to the probability that it will need to be garbage collected.

Put another way, objects that have been around for a long time are fairly unlikely to be garbage, and objects that haven’t been around for so long are considerably more likely to be garbage. By and large, this assumption proves to be true for most applications. The garbage collector leverages this fact to improve performance by implementing what are known as generations.

You can think of generations as a way of classifying objects by how long they have been alive. The garbage collector groups objects into three generations, known as generations 0, 1 and 2. Objects in generation 0 have never survived a garbage collection. They’re brand new! Objects in generation 1 have survived one garbage collection, and objects in generation 2 have survived two or more collections.

When a garbage collection begins, the garbage collector can pick and choose which generations it wishes to collect. On average, collections in generation 0 will be more effective than those in generation 1, and those in generation 1 will be more effective than those in generation 2. The garbage collector can therefore decide to only collect generation 0, for example, and hopefully reclaim a substantial amount of memory without having to collect the entire managed heap.

### Compacting

When the runtime allocates heap memory, it attempts to do so in a linear fashion. Objects are created one after another on the heap, and the runtime makes use of what is called the “next object pointer” to know where to place the next object. As you can imagine, however, once a garbage collection has taken place, the once smooth, contiguous block of memory that made up the heap is left full of holes. However, the garbage collector doesn’t leave the heap in this state*. Instead, it embarks on a compacting phase, whereby it moves objects back towards the beginning of the heap, filling the reclaimed memory spaces in the process.

As you can imagine, moving objects around is no trivial matter. When an object is moved in memory, existing references to it need to be updated. This understandably requires that program execution be suspended, as to ensure that all references to a given object are valid at all times during program execution. The CLR attempts to make this suspension as painless as possible by fine-tuning how and when garbage collection executes. Details of these techniques go beyond the scope of this article, but can be found [here on the MSDN website](http://msdn.microsoft.com/en-us/library/ee851764(v=vs.110).aspx).

### Smart invocation

The garbage collector ensures that it only collects garbage when it really needs to by setting out a memory budget for each of its three generations. These budgets can be modified by the CLR throughout program execution in order to be as well-adapted as possible to the execution conditions of a given program. When generation 0 of the managed heap surpasses its budget, the garbage collection process begins. The garbage collector checks to see if any other generations have surpassed their budgets, and then decides which generations to actually collect. This often means that garbage collection is only performed on a portion of the objects living on the managed heap, which makes the process significantly more efficient.

It is important to note that while the majority of garbage collections in an average program are invoked when generation 0 exceeds its memory budget, garbage collections can also be triggered by other events. These include the system reporting low memory conditions, the unloading of an AppDomain, the shutting down of the CLR, or a manual call to the GC.Collect method**.

## Final word

Hopefully this article will have given you a broad idea of how the garbage collector works in Microsoft’s implementation of the .NET Framework. The garbage collector is a very, very complex mechanism which could merit its own book, and this article by no means constitutes a deep dive into its inner workings. Instead, I’ve attempted to provide a simple and concise explanation of the key talking points, with hopes that it will give you a basic understanding of what goes on behind the scenes in your .NET programs.

<hr />
**Actually, it does leave the large object heap in this state. The large object heap is beyond the scope of this article, however so you won’t hear any more about it for the time being.*

_**Calling GC.Collect is discouraged by Microsoft and is generally to be avoided. The garbage collector is a very intelligent mechanism, and has much more information at its disposal than you do when it evaluates whether or not a collection is needed._

