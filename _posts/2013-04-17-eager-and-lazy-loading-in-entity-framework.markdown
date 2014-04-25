---
layout: post
title: Eager and Lazy Loading in Entity Framework
category: [Database]
tags: [c#, entity framework, performance]
comments: true
share: true
---
If you have a customer entity that looks like this…

{% highlight csharp %}
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string PhoneNumber { get; set; }
}
{% endhighlight %}

…and need to get the customer’s name, chances are you will load the entire entity from the database and merely disregard the excess data. The simplicity of this is wonderful, and the impact that the excess data retrieval and storage has on application performance is trivial. Such may not be the case however if the customer entity looks like this:

{% highlight csharp %}
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string PhoneNumber { get; set; }
    public ICollection<Purchase> Purchases { get; set; }
}
{% endhighlight %}
<a id="more"></a><a id="more-152"></a>
If the Purchases collection is rarely used during the lifetime of a given Customer object, the extra processing power and memory required to retrieve and store it may be wasteful. This cost is compounded if Purchase objects happen to be large. On the other hand, loading the collection along with the parent entity may be advantageous if it tends to play a key role in the use of a given Customer. Luckily, Entity Framework provides the developer with several different ways of managing the retrieval and storage of related entities. Two which are extremely useful to know of are called "lazy" and "explicit" loading.

# Lazy Loading

Related entities that are “lazily” loaded are by default not loaded into the parent entity when the parent is queried from the data context. The property containing the related entity will remain present within the parent, but will only be filled with data if and when it is accessed.

To implement lazy loading for a property, simply make the property virtual like so:

{% highlight csharp %}
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string PhoneNumber { get; set; }
    public virtual ICollection<Purchase> Purchases { get; set; }
}
{% endhighlight %}

Removing the virtual keyword disables lazy loading. To disable it globally for an entire data context the LazyLoadingEnabled property on the context's Configuration object needs to be set to false in the context's constructor as so:

{% highlight csharp %}
public MyDataContext()
    : base("Name=MyConnectionString")
{
    Configuration.LazyLoadingEnabled = false;
}
{% endhighlight %}

# Eager Loading

Eager loading is the opposite of lazy loading. While lazy loading waits until data is needed to query it from the database, eager loading loads it from the get-go at the same time as its parent. This guarantees that the data will be accessible without the need for a second database call.

To implement eager loading for a given property you use the include method in the query and pass it a lambda expression that points to the property that you wish to eagerly load, as follows:

{% highlight csharp %}
var customers = context.Customers
    .Include(x => x.Purchases)
    .ToList();
{% endhighlight %}

# When to Use What

The proper loading method to employ in a given situation generally depends on the following two criteria:

<ul>
<li>The probability that a given related entity will be accessed throughout the lifetime of a given entity.</li>
<li>The amount of processing power and memory required to load and maintain the related entity. This depends both on its size and the amount of effort required to retrieve it from the database.</li>
</ul>
In general, the more expensive it is to load and maintain a related entity, the more often it must be used for eager loading to make sense. In my experience lazy loading tends to be the better fit in many cases, but the two options are equally valuable tools in optimising the performance of a given Entity Framework-based application.

