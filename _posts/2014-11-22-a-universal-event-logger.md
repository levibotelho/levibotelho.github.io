---
layout: post
title: A universal event logger
category: Development
tags: [c#, expression-trees, debugging, logging, reflection]
description: Build a class that logs all the events fired by an object with the help of expression trees.
comments: true
share: true
---

When trying to fix bugs that involve objects that fire a lot of events, sometimes the best way to get a good idea of what’s happening in your program is to log what’s going on. However, attaching a logger to all the events exposed by a class can be extremely time-consuming, making this solution something of a last resort, if not virtually impossible.

In this article we’re going to build a class which will automatically attach itself to all the events exposed by a class and log when they fire and what arguments they pass.

**Note to the reader: If you're not interested in how the class works and just want to make use of the result, feel free to skip to the end of the following section where you'll find the code for the entire class all in one place.**

## Building the logger

### Identifying the events

The first step to building our event logger is discovering all of the events that are exposed by an object. Reflection makes this really easy. We simply need to make the following call, and in return we get a collection of ˋEventInfoˋ objects that tell us all we need to know.

{% highlight csharp %}
var eventInfos = o.GetType().GetEvents()
{% endhighlight %}

However now we run into a slight problem. In order to log all of these events, we need to attach methods to them that have compatible signatures. As we cannot know in advance what method signatures we’ll be dealing with, we need to generate these methods dynamically.

The way we are going to do this is by using expression trees. Expression trees represent a bit of a blind spot for many .NET developers, but once you get the hang of them they turn out to be not all that mysterious, and in fact often represent the best possible solution for generating code on the fly. In our case here, we are going to use expression trees to generate adapter methods that will collect event parameters and pass them on to a logging method.
 
### The logger class

This is the skeleton of what our logger class will look like.

{% highlight csharp %}
class EventLogger
{
    public void AttachToObject(object o)
    {
        foreach (var eventInfo in o.GetType().GetEvents())
        {
            // TODO: Call Log() using expression tree magic!
        }
    }

    public static void Log(params object[] parameters)
    {
        Console.WriteLine("Printing method parameters");
        foreach (var parameter in parameters)
            Console.WriteLine(parameter.ToString());
    }
}
{% endhighlight %}

### Writing the adapter method
The first step to writing the adapter method is to get a hold of all the parameters passed by the invoked event. Once again, refection makes this easy.

{% highlight csharp %}
var methodParameters = eventInfo.EventHandlerType.GetMethod("Invoke").GetParameters();
{% endhighlight %}

We now start building our expression tree. Expression trees are built using building blocks that represent methods, variables, decision statements, and so forth. These blocks are all linked together and compiled into code that can be executed by the CLR. Because of the way that expression tree components are linked, I find that it is often easiest to build them by thinking backwards, starting out with what you wish to accomplish, and working back until you end up at the beginning of your dynamic method.

In our case, what we want to do is pass an array of objects (the event parameters) to the `Log` method that is defined on our `EventLogger` class. The way we do this is as follows.

{% highlight csharp %}
var logExpression = Expression.Call(GetType().GetMethod("Log"), arrayInitExpression);
{% endhighlight %}

Here, we use `Expression.Call` to invoke the `Log` method defined on our current type. The second argument to `Expression.Call`, `arrayInitExpression` is an expression that defines the array of objects that we are going to pass to `Log`. We define this expression as follows

{% highlight csharp %}
var arrayInitExpression = Expression.NewArrayInit(typeof(object), boxingExpressions);
{% endhighlight %}

`Expression.NewArrayInit` is a method which creates an expression that represents initializing a one-dimensional array from a collection of objects. Essentially, you tell it with what type of objects you wish to create the array, and pass it a collection of expressions representing the objects with which you wish to fill it.

At this point we run into something of a “gotcha”. In C# there is absolutely nothing wrong with the following code

{% highlight csharp %}
var o = new object[1];
o[0] = 6;
{% endhighlight %}

However, if you take an expression representing an integer and attempt to put it into an expression representing an `object[]`, you will encounter an exception. The reason is that when the C# compiler compiles the above code, it boxes the integer `6` behind the scenes before inserting it into the array. When working with expression trees we are essentially working at the level of the compiler, and as such need to manually replicate the steps that it normally does for us automatically. Concretely, this means that we need to convert all parameters to `object` before creating an object array out of them. `boxingExpressions` in the `NewArrayInit` call represents a list of expressions that cast variables of any type to variables of the type `object`. Here is how we define those expressions.

{% highlight csharp %}
var boxingExpressions = parameterExpressions
	.Select(x => Expression.Convert(x, typeof(object))).ToArray();
{% endhighlight %}

What we are doing here is really straightforward. We are taking a collection of expressions representing our method parameters (remember those parameters are filled with variables passed by the event that invokes the method), and converting each of those parameters to `object`.

To create the `parameterExpressions` collection, we simply use the `methodParameters` collection that we obtained earlier from the event handler’s `Invoke` method.

{% highlight csharp %}
var parameterExpressions = methodParameters
	.Select(x => Expression.Parameter(x.ParameterType, x.Name)).ToArray();
{% endhighlight %}

Our expression tree is now complete! To recap, we have done the following:

1. Define a method that accepts the same parameters as those which are passed by the event that we are trying to log.
2. Cast the parameter values to `object`.
3. Put the parameter values into an array.
4. Pass the array to the `Log` method.

This has probably seemed like a lot of work just to accomplish four very simple steps. Building expression trees really gives you a new appreciation for the C# compiler, and all that it has to do to make your code runnable.

Now that we have finished our expression tree, we can now compile it into a `Delegate`, and attach that delegate to the event that we wish to log.

{% highlight csharp %}
var handlerDelegate = Expression.Lambda(logExpression, parameterExpressions).Compile();
eventInfo.AddEventHandler(o, handlerDelegate);
{% endhighlight %}

When put all together, our event logger class looks like the following.

{% highlight csharp %}
class EventLogger
{
    public void AttachToObject(object o)
    {
        foreach (var eventInfo in o.GetType().GetEvents())
        {
            var methodParameters = eventInfo.EventHandlerType.GetMethod("Invoke")
		.GetParameters();
            var parameterExpressions = methodParameters
		.Select(x => Expression.Parameter(x.ParameterType, x.Name))
		.ToArray();
            var boxingExpressions = parameterExpressions
		.Select(x => Expression.Convert(x, typeof(object))).ToArray();
            var arrayInitExpression =
		Expression.NewArrayInit(typeof(object), boxingExpressions);
            var logExpression =
		Expression.Call(GetType().GetMethod("Log"), arrayInitExpression);
            var handlerDelegate =
		Expression.Lambda(logExpression, parameterExpressions).Compile();
            eventInfo.AddEventHandler(o, handlerDelegate);
        }
    }

    public static void Log(params object[] parameters)
    {
        Console.WriteLine("Printing method parameters");
        foreach (var parameter in parameters)
            Console.WriteLine(parameter.ToString());
    }
}
{% endhighlight %}

## Testing it out
To test our event logger, we can use the following program:

{% highlight csharp %}
class Program
{
    static void Main(string[] args)
    {
        var e = new EventFirer();
        var l = new EventLogger();
        l.AttachToObject(e);
        e.Fire();
        Console.WriteLine("Done");
        Console.ReadLine();
    }
}

class EventFirer
{
    public event Action<int> Event1 = delegate { };
    public event Action<double, bool, string> Event2 = delegate { };

    public void Fire()
    {
        Event1(1);
        Event2(2.2, true, "hello world");
    }
}
{% endhighlight %}

The output to the console window is as follows:

{% highlight text %}
Printing method parameters
1
Printing method parameters
2.2
True
hello world
Done
{% endhighlight %}
