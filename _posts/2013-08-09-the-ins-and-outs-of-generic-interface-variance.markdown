---
layout: post
title: The ins and outs of generic interface variance
category: C#
tags: [c#, clr]
comments: true
share: true
---
In this post we’re going to look at generic interface polymorphism in C#. Let’s start by defining a couple of example classes.

{% highlight csharp %}
public class Message
{
    public virtual string Text { get; set; }
}

public class DatedMessage : Message
{
    public DateTime DateReceived { get; set; }
}
{% endhighlight %}

We’ll also define an interface that serves as a queue to hold messages.

{% highlight csharp %}
public interface IMessageQueue<T>
{
    void Add(T message);
    T Read();
}
{% endhighlight %}

And finally we’ll write two implementations of IMessageQueue. One for Message objects and one specifically for DatedMessage objects.
{% highlight csharp %}
public class MessageQueue : IMessageQueue<Message>
{
    readonly Queue<Message> messages = new Queue<Message>();

    public void Add(Message message)
    {
        messages.Enqueue(message);
    }

    public Message Read()
    {
        return messages.Dequeue();
    }
}

public class DatedMessageQueue : IMessageQueue<DatedMessage>
{
    readonly Queue<DatedMessage> messages = new Queue<DatedMessage>();

    public void Add(DatedMessage message)
    {
        messages.Enqueue(message);
    }

    public DatedMessage Read()
    {
        return messages.Dequeue();
    }
}
{% endhighlight %}

Enough preparation, let’s get going.<a id="more"></a><a id="more-2092"></a> If we take a look at our MessageQueue class, we can easily see that it can hold both Message and DatedMessage objects. This makes perfect sense, because all DatedMessages are by definition Messages. Taking this one step further, we should therefore be allowed to do the following.

{% highlight csharp %}
static void Main(string[] args)
{
    var datedMessages = new DatedMessageQueue();
    datedMessages.Add(new DatedMessage("Hello world", DateTime.Now));
    var messages = (IMessageQueue<Message>)datedMessages;
    // ...
}
{% endhighlight %}

However, while this code compiles, an invalid cast exception is thrown when we attempt to make our cast. But why? As all DatedMessages are Messages, an implementation of IMessageQueue<DatedMessage> should equally be treatable as an implementation of IMessageQueue<Message>, shouldn’t it?

In fact the answer to this question is no, and it’s not terribly difficult to see why. If this cast were legal, then nothing would prevent us from doing the following.

{% highlight csharp %}
static void Main(string[] args)
{
    var datedMessages = new DatedMessageQueue();
    datedMessages.Add(new DatedMessage("Hello world", DateTime.Now));
    var messages = (IMessageQueue<Message>)datedMessages;
    messages.Add(new Message("Hello Message"));
    //...
}
{% endhighlight %}

While all DatedMessages are also Messages, not all Messages are DatedMessages. If the above code were allowed to execute, we would be attempting to add a Message to what is in reality a DatedMessage-typed backing store—that is, a DatedMessageQueue that uses a Queue<DatedMessage> to internally hold its messages.

But what if you really do want to be able to perform this cast? Well, there is indeed a way to allow it. We simply need to guarantee that we will never add any additional elements to an IMessageQueue. We do this by removing our “Add” method, and by applying the “out” keyword to our generic parameter.

{% highlight csharp %}
public interface IMessageQueue<out T>
{
    T Read();
}
{% endhighlight %}

The out keyword indicates that objects of type T will only ever come out of an instance of the interface. More specifically, it prohibits us from implementing any methods which take an instance of T as an input parameter. Note that nothing prevents us from adding a message to an instance of our MessageQueue or DatedMessageQueue classes (which is why logically we cannot by default cast a DatedMessageQueue to a MessageQueue). We simply cannot add a message to an instance of IMessageQueue itself.

So, what have we seen so far? Well, we’ve seen that by default a generic interface cannot be downcast to less-specific versions of itself due to the risk of an incompatible type being added to an internal backing store. However we’ve also seen that by forbidding any such addition by way of the out keyword we can allow such downcasting to occur.

This concept of downcasting is called *covariance</i>, as the direction of the cast goes along with the standard inheritance flow of more specific objects being treated as more general objects. To be precise, we say that the out keyword makes IMessageQueue <i>covariant in T*.

Now, let’s code a new interface, this time for a class which will write the same text to a bunch of message objects.

{% highlight csharp %}
public interface IBatchMessageWriter<T>
{
    void AddToBatch(T message);
    void Write(string text);
    List<T> GetMessages();
}

public class BatchMessageWriter : IBatchMessageWriter<Message>
{
    readonly List<Message> messages = new List<Message>();

    public void AddToBatch(Message message)
    {
        messages.Add(message);
    }

    public void Write(string text)
    {
        foreach (var message in messages)
        {
            message.Text = text;
        }
    }

    public List<Message> GetMessages()
    {
        return messages;
    }
}
{% endhighlight %}

Now, because we can add any Message object to our Batch Message Writer, it logically follows that we can equally add any object whose class derives from Message. After all, a Message derivative is still a Message at its core. Going one step further with this logic, we should then therefore be able to do the following.

{% highlight csharp %}
static void Main(string[] args)
{
    var writer = new BatchMessageWriter();
    var datedMessageWriter = (IBatchMessageWriter<DatedMessage>)writer;
    // ...
}
{% endhighlight %}

But just as in our first example, this code throws an invalid cast exception—and for a similar reason. If this cast were allowed, nothing would prevent us from calling GetMessages, which would return a List of DatedMessage objects. If we had previously added any plain Message objects to the interface implementation by way of the Add method, we would be attempting to cast these Message objects to DatedMessage objects, which is not allowed.

However once again, there is a way to permit this cast. We simply need add the “in” keyword to the generic type argument “T” in the interface definition. “in” is the opposite of “out”, in as much as it indicates that objects of type T can only be used as input parameters to the interface. It therefore prohibits us from defining any method on the interface which returns T. So if we want the above cast to be legal, we need to modify our IBatchMessageWriter interface as follows.

{% highlight csharp %}
public interface IBatchMessageWriter<in T>
{
    void AddToBatch(T message);
    void Write(string text);
}
{% endhighlight %}

It is important to note that, as with “out”, “in” doesn’t prevent us from adding a method to any class which implements IBatchMessageWriter that returns an object of type T. It simply prevents us from defining such a method on the interface itself.

So, to summarise, we’ve seen that applying the “in” keyword to a generic type parameter on an interface allows the interface to be upcast to a more-specific version of itself. This makes sense, because a function that takes type T as input should also be able to take an object of any type that inherits from T. The concept of a type being treatable as a more specific version of itself is known as *contravariance</i>, because it goes <i>counter* to the traditional inheritance flow where objects are treated as less-specific versions of themselves.

