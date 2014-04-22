---
layout: post
status: publish
published: true
title: Aggregate Explained
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "For my money, LINQ is in itself a reason to program in C#. It saves time,
  reduces the amount of code needed to perform a given task, and is simple to debug.
  A function I really like is <strong>Aggregate</strong>. That said, I admit that
  it took a while for me to adopt it into my coding repertoire because its name doesn't
  do a very good job at describing what it does or how to use it.\r\n<h2>What does
  it do?</h2>\r\nAggregate takes a collection of small objects and combines them one
  by one into a single large object. It can take an optional “seed” value to which
  all the objects are added as well as a “selector” function which applies a final
  transformation on the combined object.\r\n<h2>How is it used?</h2>\r\nThe definition
  of Aggregate in its simplest form looks like this:\r\n"
wordpress_id: 72
wordpress_url: http://levibotelho.azurewebsites.net/?p=72
date: !binary |-
  MjAxMy0wNC0xNyAwODowNToyMSArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODowNToyMSArMDIwMA==
categories:
- C#
tags:
- c#
- linq
comments: []
---
<p>For my money, LINQ is in itself a reason to program in C#. It saves time, reduces the amount of code needed to perform a given task, and is simple to debug. A function I really like is <strong>Aggregate</strong>. That said, I admit that it took a while for me to adopt it into my coding repertoire because its name doesn't do a very good job at describing what it does or how to use it.</p>
<h2>What does it do?</h2>
<p>Aggregate takes a collection of small objects and combines them one by one into a single large object. It can take an optional “seed” value to which all the objects are added as well as a “selector” function which applies a final transformation on the combined object.</p>
<h2>How is it used?</h2>
<p>The definition of Aggregate in its simplest form looks like this:<br />
<a id="more"></a><a id="more-72"></a><br />
[sourcecode language="csharp"]<br />
collection.Aggregate(Func&lt;TSource, TSource, TSource&gt;)<br />
[/sourcecode]</p>
<p>As you can see, the Func passed to Aggregate takes two variables. The first represents the “accumulated” value and the second represents the given element in the collection. A simple way to demonstrate this is by using it to sum a collection of numbers.</p>
<p>[sourcecode language="csharp"]<br />
var numbers = new int[] { 1, 2, 3, 4 };<br />
var sum = numbers.Aggregate((accumulated, number) =&gt; accumulated +<br />
number);<br />
Console.WriteLine(sum); // Returns 10<br />
[/sourcecode]</p>
<p>The aggregation starts with an empty result (which in this case is <em>default(int)</em>, or 0) and adds the first number of the collection to it. The result then becomes the accumulated value to which the second number is added in the following iteration. This continues until there are no more elements.</p>
<p>A seed value can optionally be passed to Aggregate to give it an initial value to which the first element in the collection is added. For example:</p>
<p>[sourcecode language="csharp"]<br />
var numbers = new int[] { 1, 2, 3, 4 };<br />
var sum = numbers.Aggregate(10, (accumulated, number) =&gt; accumulated + number);<br />
Console.WriteLine(sum); // Returns 20<br />
[/sourcecode]</p>
<p>Finally, a “selector” function can be passed to Aggregate which is executed on the final result of the aggregation before it is returned. To demonstrate, if I wanted to return the string representation of the above function, I could do this as follows:</p>
<p>[sourcecode language="csharp"]<br />
var numbers = new int[] { 1, 2, 3, 4 };<br />
var sum = numbers.Aggregate(10, (accumulated, number) =&gt; accumulated + number, x =&gt; x.ToString());<br />
Console.WriteLine(sum); // Returns &quot;20&quot;<br />
[/sourcecode]</p>
<h2>A more complex implementation</h2>
<p>Let’s imagine that we are given a collection of integers and that we want to return a string containing all of them as a comma-separated list. We also want to use a StringBuilder to do the actual combining. Here is how it can be done:</p>
<p>[sourcecode language="csharp"]<br />
var numbers = new int[] { 1, 2, 3, 4 };<br />
var sum = numbers.Aggregate(<br />
    new StringBuilder(),<br />
    (x, y) =&gt;<br />
    {<br />
        x.Append(y);<br />
        return x.Append(&quot;, &quot;);<br />
    },<br />
    x =&gt;<br />
    {<br />
        var result = x.ToString();<br />
        return result.Remove(result.LastIndexOf(&quot;,&quot;));<br />
    });<br />
Console.WriteLine(sum); // returns &quot;1, 2, 3, 4&quot;<br />
[/sourcecode]</p>
<p>The first argument to the function specifies a new StringBuilder object as the seed value. The second argument which is executed for each number in our collection appends the number to the StringBuilder as well as a comma and a space. Note that we need to return the StringBuilder each time so that it can be used in the following iteration. The final argument removes the last comma and space from the resulting string and returns the result.</p>
<h2>A small disclaimer...</h2>
<p>The above example is useful for understanding how Aggregate works. There is however, a far more concise way to achieve the same result:</p>
<p>[sourcecode language="csharp"]<br />
sum = string.Join(&quot;, &quot;, numbers);<br />
[/sourcecode]</p>
