[C#] Distinct() with IEqualityComparer not working

A colleague of mine came across this situation the other day. He was trying to filter a list of doubles using an "almost equals" method which compared two doubles for equality within the range of a certain epsilon. Pretty standard stuff.

The parameterless implementation of `Distinct` compares elements using the default equality comparer. However there is a relatively little-used overload of `Distinct` that takes an `IEqualityComparer` as a property. While a predicate/lambda would be much more practical, the `IEqualityComparer` overload seemed to do the trick.

He implemented the comparer as a nested class like so.

{% highlight csharp %} 

{% endhighlight %}

Looks good. However when he ran the code he found that virtually all of the values passed to the method were being treated as distinct and making it into the output collection!

Finding out why this occurred took a bit of digging. We ended up finding the problem thanks to Reference Source. It turns out that `Distinct(this IEnumerable<TSource> source, IEqualityComparer<TSource> comparer)` compares two values in quite a surprising way. Instead of doing this...

{% highlight csharp %} 

{% endhighlight %}

It does the equivalent of this...

{% highlight csharp %} 
comparer.GetHashCode(value1) == comparer.GetHashCode(value1) &&
comparer.Equals(value1, value2)
{% endhighlight %}

Implicitly, the overload of `Distinct` which takes a comparer calls both the comparison function *and* the hash code function to check for equality. Two objects must have the same values for *both* methods in order to be considered equal.

This is pretty surprising behaviour if you ask me. I consider it a design failure on the part of the .NET team to have implemnted the method like this. There is probably an enlightening backstory which explains why the method behaves as it does, but at the very least they could have provided an overload which takes a `Func<T, T, bool>` to by default lead developers down the simplest path.

The other problem that I have with this implementation is that the knee-jerk reaction to this problem is to simply modify `GetHashCode` to always return a set value. This could provide a short-term solution, as `Distinct` would therefore always rely on the comparison function to check for equality, however I strongly advise against this solution for two reasons.

1. GetHashCode() is expected to behave in a very specific way, providing distinct hash values for unique objects. Going against this risks correcting the bug at the expense of confusing developers down the line.
2. This correction fixes the current problem, but we have absolutely no guarantee that this overload of `Distinct` will function in this way in the future. The fact that it does is just an implementation detail, and if in the future the .NET team decides to change this, we could be introducing a bug somewhere down the line that future developers will have an even harder time tracking down.

If you want to check out the actual code for `Distinct`, you can get that [here](http://referencesource.microsoft.com/#System.Core/System/Linq/Enumerable.cs#1246b23904e29c42#references).

The solution he ended up adopting in the end was to create an output list of accepted items and run through each input item one by one, checking the output list for duplicates using `Any` to which he passed a predicate which called the comparison function. While this isn't hyper-optimised like `Distinct`, it does the job. I mention this because the knee-jerk rea