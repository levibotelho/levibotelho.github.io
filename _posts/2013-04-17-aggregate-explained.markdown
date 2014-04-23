---
layout: post
title: "Some Title"                 # Title of the post
description: Some description       # Description of the post, used for Facebook Opengraph & Twitter
headline: Some headline             # Will appear in bold letters on top of the post
category: personal
tags: []
image: 
  feature: some-image.jpg
comments: true
mathjax:
---

For my money, LINQ is in itself a reason to program in C#. It saves time, reduces the amount of code needed to perform a given task, and is simple to debug. A function I really like is **Aggregate**. That said, I admit that it took a while for me to adopt it into my coding repertoire because its name doesn't do a very good job at describing what it does or how to use it.

## What does it do?

Aggregate takes a collection of small objects and combines them one by one into a single large object. It can take an optional “seed” value to which all the objects are added as well as a “selector” function which applies a final transformation on the combined object.

## How is it used?

The definition of Aggregate in its simplest form looks like this:
<a id="more"></a><a id="more-72"></a>
{% highlight csharp %}
collection.Aggregate(Func<TSource, TSource, TSource>)
{% endhighlight %}

As you can see, the Func passed to Aggregate takes two variables. The first represents the “accumulated” value and the second represents the given element in the collection. A simple way to demonstrate this is by using it to sum a collection of numbers.

{% highlight csharp %}
var numbers = new int[] { 1, 2, 3, 4 };
var sum = numbers.Aggregate((accumulated, number) => accumulated +
number);
Console.WriteLine(sum); // Returns 10
{% endhighlight %}

The aggregation starts with an empty result (which in this case is *default(int)*, or 0) and adds the first number of the collection to it. The result then becomes the accumulated value to which the second number is added in the following iteration. This continues until there are no more elements.

A seed value can optionally be passed to Aggregate to give it an initial value to which the first element in the collection is added. For example:

{% highlight csharp %}
var numbers = new int[] { 1, 2, 3, 4 };
var sum = numbers.Aggregate(10, (accumulated, number) => accumulated + number);
Console.WriteLine(sum); // Returns 20
{% endhighlight %}

Finally, a “selector” function can be passed to Aggregate which is executed on the final result of the aggregation before it is returned. To demonstrate, if I wanted to return the string representation of the above function, I could do this as follows:

{% highlight csharp %}
var numbers = new int[] { 1, 2, 3, 4 };
var sum = numbers.Aggregate(10, (accumulated, number) => accumulated + number, x =&gt; x.ToString());
Console.WriteLine(sum); // Returns "20&quot;
{% endhighlight %}

## A more complex implementation

Let’s imagine that we are given a collection of integers and that we want to return a string containing all of them as a comma-separated list. We also want to use a StringBuilder to do the actual combining. Here is how it can be done:

{% highlight csharp %}
var numbers = new int[] { 1, 2, 3, 4 };
var sum = numbers.Aggregate(
    new StringBuilder(),
    (x, y) =>
    {
        x.Append(y);
        return x.Append(", &quot;);
    },
    x =>
    {
        var result = x.ToString();
        return result.Remove(result.LastIndexOf(",&quot;));
    });
Console.WriteLine(sum); // returns "1, 2, 3, 4&quot;
{% endhighlight %}

The first argument to the function specifies a new StringBuilder object as the seed value. The second argument which is executed for each number in our collection appends the number to the StringBuilder as well as a comma and a space. Note that we need to return the StringBuilder each time so that it can be used in the following iteration. The final argument removes the last comma and space from the resulting string and returns the result.

## A small disclaimer...

The above example is useful for understanding how Aggregate works. There is however, a far more concise way to achieve the same result:

{% highlight csharp %}
sum = string.Join(", &quot;, numbers);
{% endhighlight %}

