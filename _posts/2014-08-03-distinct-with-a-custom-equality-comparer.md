---
layout: post
title: Distinct with a custom equality comparer
category: Development
tags: [c#]
description: A look at how to compare two collections using a custom equality comparer. It's not as easy as it may seem!
comments: true
share: true
---

The other day a colleague of mine was trying to get distinct values out of a list of doubles. He wanted two doubles to be considered equal if the difference between them was less than 0.1.

C# does not offer a `Distinct` overload that takes a `Func<T, T, bool>` to handle the comparison. It does, however, offer one which takes an `IEqualityComparer`. He decided to make use of this overload and implemented something that looked roughly like the following.

{% highlight csharp %} 
static void Main(string[] args)
{
    var doubles = new[] {1, 1.01, 2, 2.01, 3 };
    var distinctDoubles = doubles.Distinct(new DoubleComparer());
    foreach (var distinctDouble in distinctDoubles)
        Console.WriteLine(distinctDouble);
    Console.ReadLine();
}

class DoubleComparer : EqualityComparer<double>
{
    public override bool Equals(double x, double y)
    {
        return Math.Abs(x - y) < 0.1;
    }

    public override int GetHashCode(double obj)
    {
        return obj.GetHashCode();
    }
}
{% endhighlight %}

However instead of returning the expected output.

{% highlight text %}
1
2
3
{% endhighlight %}

His implementation returned the following.

{% highlight text %}
1
1.01
2
2.01
3
{% endhighlight %}

The answer to why this happened is hidden in a two-line comment in the Examples section of the [`Enumerable.Distinct` MSDN page](http://msdn.microsoft.com/en-us/library/bb338049(v=vs.110).aspx). Specifically, it states that

> If Equals() returns true for a pair of objects then GetHashCode() must return the same value for these objects.

A quick check of [the Reference Source entry for the method](http://referencesource.microsoft.com/#System.Core/System/Linq/Enumerable.cs#1246b23904e29c42#references) confirms that in order for two elements to be considered equal, `Equals` must return true *and* `GetHashCode` must return the same value for both elements.

I found this behaviour to be pretty surprising. Although it is a well-known convention that equal objects should have the same hash code, when one is supplying a custom equality comparer, it is probably safe to say that the objects would not typically be considered to be equal to one another.

Perhaps the most disturbing facet of this implementation, however, is that it is notoriously easy to do the following in order to get the desired result.

{% highlight csharp %} 
class DoubleComparer : EqualityComparer<double>
{
    public override bool Equals(double x, double y)
    {
        return Math.Abs(x - y) < 0.1;
    }

    public override int GetHashCode(double obj)
    {
        return 0;
    }
}
{% endhighlight %}

While this implementation will work, it relies on the fact that `Distinct` will always use `Equals` *and* `GetHashCode` to make the comparison. As we cannot guarantee that this will remain the case in future versions of the .NET Framework, I feel that this type of implementation is a bit too hacky for my liking, and should be avoided.

So, how can we get the result that we're after without compromising good design principles? Two simple solutions come to mind. The first thing that we could do is simply brute-force the problem.

{% highlight csharp %}
public static List<T> DistinctBruteForce<T>(
	this IEnumerable<T> source, Func<T, T, bool> comparer)
{
    var result = new List<T>();
    foreach (var sourceElement in source)
    {
        if (!result.Any(resultElement => comparer(sourceElement, resultElement)))
            result.Add(sourceElement);
    }
    return result;
}
{% endhighlight %}

The obvious issue with this method is that it will execute in O(n²) time.

An alternative solution is to sort the list first.

{% highlight csharp %}
public static List<T> DistinctSorted<T>(
	this IEnumerable<T> source, Func<T, T, bool> areEqual)
{
    source = source.OrderBy(x => x);
    var lastElement = default(T);
    var result = new List<T>();
    foreach (var sourceElement in source)
    {
        if (!areEqual(sourceElement, lastElement) || !result.Any())
        {
            result.Add(sourceElement);
            lastElement = sourceElement;
        }
    }
    return result;
}
{% endhighlight %}

This will execute in the same O as our sort, which we can generally take to mean O(n*log(n)).

The one important difference between these two solutions, aside from the execution time, is that `DistinctBruteForce` will return distinct elements in the order that they appear in the source collection, whereas `DistinctSorted` will add them in order from smallest to largest. We can see this difference if we modify our method to take a different collection, and to use all three comparison methods (hacky `IEqualityComparer`, `DistinctBruteForce`, and `DistinctSorted`).

{% highlight csharp %}
static void Main(string[] args)
{
    var doubles = new[] {1.01, 1, 1.1, 2 };
    var iEqualityComparer = doubles.Distinct(new DoubleComparer());
    var bruteForce = doubles.DistinctBruteForce((x, y) => Math.Abs(x - y) < 0.1);
    var sorted = doubles.DistinctSorted((x, y) => Math.Abs(x - y) < 0.1);

    Console.WriteLine("IEqualityComparer");
    foreach (var distinctDouble in iEqualityComparer)
        Console.WriteLine(distinctDouble);
    Console.WriteLine();

    Console.WriteLine("Brute Force");
    foreach (var distinctDouble in bruteForce)
        Console.WriteLine(distinctDouble);
    Console.WriteLine();

    Console.WriteLine("Sorted");
    foreach (var distinctDouble in sorted)
        Console.WriteLine(distinctDouble);
    Console.ReadLine();
}
{% endhighlight %}

The console output obtained by running this is as follows.

{% highlight text %}
IEqualityComparer
1.01
2

Brute Force
1.01
2

Sorted
1
1.1
2
{% endhighlight %}

As you can see, the `Sorted` implementation includes `1` and `1.1`, whereas the other two only include the middle value, `1.01`. While in many cases I would expect the `Sorted` result to be the more desirable the two, knowing that these implementations give different results could no doubt prove to be useful when having to deal with a problem such as this.