---
layout: post
title: ! '[C#] Compare generic variables for equality'
category: Development
tags: [c#, generics]
comments: true
share: true
---
This is a simple task, yet one which is frequently carried out incorrectly. This kind of comparison is often required when dealing with classes that look like the following.

{% highlight csharp %}
class GenericContainer<T>
{
    T _variable;

    public T Variable
    {
        get { return _variable; }
        set
        {
            // Test for equality before setting.

            _variable = value;
            // Invoke an event here...
        }
    }
}
{% endhighlight %}

Now the knee-jerk reaction to compare `_variable` and `value` in this case is to do this.

{% highlight csharp %}
set
{
    if (_variable == value)
        return;

    _variable = value;
    // Monkey business...
}
{% endhighlight %}

However, this doesn’t compile. This makes sense, because we cannot guarantee that the type `T` overloads the equality operator (reference types do by default, but value types do not). In light of this problem, many people will resort to the following.

{% highlight csharp %}
set
{
    if (_variable.Equals(value))
        return;

    _variable = value;
    // Monkey business...
}
{% endhighlight %}

However this poses a different problem. If `_variable` is ever null, your program will throw a null reference exception. But there is a way around this. Simply use the static implementation of `Equals()` on `System.Object` as follows.

{% highlight csharp %}
set
{
    // We don’t even need to write object.Equals
    // because we are currently on an object.
    if (Equals(_variable, value))
        return;

    _variable = value;
    // Monkey business.
}
{% endhighlight %}

Problem solved!