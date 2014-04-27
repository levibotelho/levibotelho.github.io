---
layout: post
title: When should a C# method be made virtual?
category: Development
tags: [c#]
comments: true
share: true
redirect_from: "/when-should-a-c-method-be-made-virtual/"
---
[In the last post](http://www.levibotelho.com/polymorphism-with-new-and-override/) we saw how the behaviour of base and derived classes changes with the presence of the new or override keyword on inherited methods. In this post we’re going to see the impact that this has on class design. Let’s start with some code:

{% highlight csharp %}
static void Main(string[] args)
{
    var input = new DerivedClass();
    WriteClassName(input); // Outputs BaseClass
    Console.ReadLine();
}

static void WriteClassName(BaseClass input)
{
    input.WriteName();
}

class BaseClass
{
    public void WriteName()
    {
        Console.WriteLine(GetName());
    }

    protected string GetName()
    {
        return "BaseClass";
    }
}

class DerivedClass : BaseClass
{
    protected new string GetName()
    {
        return "DerivedClass";
    }
}
{% endhighlight %}

This is a lot like the code we saw last week. The main difference is that here the exposed method is “WriteName”, which calls the class’s “GetName” method to get the class name. When run, this program outputs "BaseClass", which is to be expected given what we learned last time around.<a id="more"></a><a id="more-1632"></a> Similarly, if we modify our classes as such:

{% highlight csharp %}
class BaseClass
{
    public void WriteName()
    {
        Console.WriteLine(GetName());
    }

    protected virtual string GetName()
    {
        return "BaseClass";
    }
}

class DerivedClass : BaseClass
{
    protected override string GetName()
    {
        return "DerivedClass";
    }
}
{% endhighlight %}

It should be equally unsurprising that this outputs “DerivedClass”.

But let’s take a moment to think about what has happened here. In both cases, BaseClass.WriteName() is called, which then goes on to call the GetName() method. However while BaseClass.GetName() is called when GetName() is nonvirtual, DerivedClass.GetName() is called when it is. What this shows us is that *making a method virtual allows the functionality of the base class to be altered in a derivation of the class.*

The impact of this is far-reaching and shouldn't be underestimated. While it can be very useful to override base class logic (think Object.ToString()), in many cases base classes encapsulate functionality that shouldn’t be modifiable. A good example of this is `ClientBase<T>`, which serves as the base class for WCF client classes. While `ClientBase<T>` is designed to be inherited in order to generate clients for various WCF services, derived classes should not be able to impact the core logic required to construct (or teardown) a WCF client altogether. It is the responsibility of the developer of a class to ensure that a it remains functionally robust when it is used in the future. The developer writing an inheriting class should only be concerned about correctly implementing their own functionality, and should not have to concern themselves with potentially breaking the base class when they implement their own methods.\*

So, while virtual methods are a key ingredient in extensible class design, one has to keep in mind that they open base class functionality up to modification\*\*. If this is not explicitly desired, then a method should not be made virtual. Non-virtual methods can still be hidden by use of the new keyword, which exists specifically to allow a method to be replaced in a derived class, while keeping the base class functionality intact.

<hr />
\* This is the exact same concept as that which applies to access modifiers.

\*\* Virtual methods also tend to slightly underperform nonvirtual methods for several reasons which are beyond the scope of this post. While this performance impact is trivial in many cases, in high-performance scenarios it may be worthy of consideration.

