---
layout: post
title: The performance advantage of generic type constraints
category: C#
tags: [c#, clr, performance, generics]
comments: true
share: true
---
Have a look at the following two methods.

{% highlight csharp %}
static int CompareToSix(IComparable<int> input)
{
    return input.CompareTo(6);
}

static int CompareToSixGeneric<T>(T input)
    where T : IComparable<int>
{
    return input.CompareTo(6);
}
{% endhighlight %}

Functionally they aren’t much different. Both take an `IComparable<int>`, compare it to the number six, and then return the result. However when I execute the following code which calls the two methods 100,000,000 times each and compares the running time:

{% highlight csharp %}
static void Main(string[] args)
{
    var sw = new Stopwatch();

    sw.Start();

    for (var i = 0; i < 100000000; i++)
        CompareToSix(2);

    sw.Stop();
    Console.WriteLine("Non-Generic: " + sw.ElapsedMilliseconds + "ms");
    sw.Restart();

    for (var i = 0; i < 100000000; i++)
        CompareToSixGeneric(2);

    sw.Stop();
    Console.WriteLine("Generic: " + sw.ElapsedMilliseconds + "ms");
    Console.ReadLine();
}
{% endhighlight %}

I get the following result.\*

>Non-Generic: 1214ms
>Generic: 512ms

Weird, eh?

The reason why this occurs is due to the way in which the `Compare` method is called. The difference really boils down to the fact that

{% highlight csharp %}
int comparable = 2;
comparable.CompareTo(6);
{% endhighlight %}

which is functionally similar to the `CompareToSix` method is not the same as

{% highlight csharp %}
IComparable<int> comparable = 2;
comparable.CompareTo(6);
{% endhighlight %}

which is functionally similar to the `CompareSixGeneric` method.

The difference between the two is that when we cast a value type to an interface implementation, a boxing operation is necessary. However when we simply execute an interface method on a value type, no boxing is required. `CompareToSix` which takes an `IComparable<int>` invokes this boxing operation, whereas `CompareToSixGeneric` which takes a `T : IComparable<int>` does not. As proof, we can take a look at the IL for the below code which calls both methods.

{% highlight csharp %}
static void Main(string[] args)
{
    CompareToSix(2);
    CompareToSixGeneric(2);
}
{% endhighlight %}

And the IL...

{% highlight text %}
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       20 (0x14)
  .maxstack  8
  IL_0000:  ldc.i4.2
  IL_0001:  box        [mscorlib]System.Int32 // Boxing here...
  IL_0006:  call       int32 ConsoleApplication9.Program::CompareToSix
  IL_000b:  pop
  IL_000c:  ldc.i4.2
  // ...but not here!
  IL_000d:  call       int32 ConsoleApplication9.Program::CompareToSixGeneric
  IL_0012:  pop
  IL_0013:  ret
} // end of method Program::Main
{% endhighlight %}

Note that as boxing only impacts value types, we would not observe such a performance difference if we passed a reference type to either method. It is also important to keep in mind that in my example above there was a performance hit of approximately 700ms when looping through the operation 100,000,000 times. The real performance impact of this difference in the vast majority of applications will therefore be insignificant. I'll even go one step further and state that 99% of the time you will be better off choosing the method which is the most readable and makes the most sense in the context of your solution, rather than the method which is better performing. All that aside however, you still may come across a situation where application performance is critical, and in such a scenario it is important to take heed to the impact that unnecessary boxing such as that found here can have on an application.

---

\* Although it should be obvious, I'll go ahead and state that these are the results that I obtained on my machine and that while you should in principle see the same behaviour on your machine, YMMV.

