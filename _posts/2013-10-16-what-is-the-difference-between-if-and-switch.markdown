---
layout: post
title: What is the difference between if and switch?
category: Development
tags: [c#, clr, performance]
description: If and switch statements are not necessarily syntactic sugar for one another. Learn about the fundamental difference between the two.
comments: true
share: true
redirect_from: "/what-is-the-difference-between-if-and-switch/"
---
Although switch and if may seem to be two nearly-equivalent representations of the same concept, under the hood they function in two very different ways. Understanding the differences between the two makes for good general knowledge and provides one with an interesting glimpse at how the C# compiler works to optimise your code without you even knowing it.

## If/else statements

If/else statements at the IL level work pretty much as one would expect them to. The runtime evaluates each expression in a series until it finds one which is true, at which it jumps to and executes the code corresponding to the case. To illustrate, let’s look at the IL for the following code.

{% highlight csharp %}
if (val == 1)
	Console.WriteLine("One");
else if (val == 2)
	Console.WriteLine("Two");
else if (val == 3)
	Console.WriteLine("Three");
{% endhighlight %}

{% highlight text %}
IL_000E:  ldloc.0     // Load our “val” value onto the stack
IL_000F:  ldc.i4.1    // Load the numeric value 1 onto the stack
IL_0010:  bne.un.s    IL_001D    // Go to line IL_001D if the two
                                 // values on the stack are NOT equal,
                                 // otherwise execute the following...
IL_0012:  ldstr       "One"
IL_0017:  call        System.Console.WriteLine
IL_001C:  ret
// The remaining code simply repeats the above pattern...
IL_001D:  ldloc.0     // val
IL_001E:  ldc.i4.2
IL_001F:  bne.un.s    IL_002C
IL_0021:  ldstr       "Two"
IL_0026:  call        System.Console.WriteLine
IL_002B:  ret
IL_002C:  ldloc.0     // val
IL_002D:  ldc.i4.3
IL_002E:  bne.un.s    IL_003A
IL_0030:  ldstr       "Three"
IL_0035:  call        System.Console.WriteLine
{% endhighlight %}

Nothing much to say about this really, this is pretty simple stuff. That said, it’s important to see how if statements function so that we have something to compare switch to. Let’s go ahead and take a look at how a switch statement accomplishes the same task.

## Switch statements

Let’s dive right in by rewriting the above example as a switch statement and examining the corresponding IL.

{% highlight csharp %}
switch (val)
{
	case 1:
		Console.WriteLine("One");
		break;
	case 2:
		Console.WriteLine("Two");
		break;
	case 3:
		Console.WriteLine("Three");
		break;
}
{% endhighlight %}

{% highlight text %}
IL_0002:  ldloc.0     // Load our “val” value onto the stack
IL_0003:  stloc.1     // CS$0$0000
IL_0004:  ldloc.1     // CS$0$0000
IL_0005:  ldc.i4.1
IL_0006:  sub
IL_0007:  switch      (IL_0019, IL_0024, IL_002F)
IL_0018:  ret
IL_0019:  ldstr       "One"
IL_001E:  call        System.Console.WriteLine
IL_0023:  ret
IL_0024:  ldstr       "Two"
IL_0029:  call        System.Console.WriteLine
IL_002E:  ret
IL_002F:  ldstr       "Three"
IL_0034:  call        System.Console.WriteLine
{% endhighlight %}

There are two really noticeable differences here in comparison to the switch. First, there is the presence of the “switch” right there in the middle of the code. And second, there are no equality, inequality, greater than or less than comparisons to be seen. So if switch doesn’t work by comparing sets of values, how does it correctly manage to route program flow?<a id="more"></a><a id="more-2532"></a>

Switch does this by implementing what is known as a branch table. You can think of a branch table as an array of locations in the program to which to navigate. These locations, known formally as “targets”, are what we see in parentheses beside the switch statement in the above IL. Switch selects the target to direct the program flow to by using a zero-based index which it simply pops off the stack. The genius of switch, however, is that the index is mathematically calculated from the value passed to the switch statement in C#, and not derived by evaluating a series of “if equals” statements. This allows switch to be consistently fast no matter how many cases it may contain. Where if may need to evaluate every single case in a long list in order to find the case that turns out to be true, switch simply calculates the branch table index and forges on. What is really neat about all this is that the calculation to determine the branch table index is visible directly in the IL of C# program. Let’s take another look at the above IL and see how it’s done in this case.

{% highlight text %}
IL_0002:  ldloc.0     // Load our “val” value onto the stack
IL_0003:  stloc.1     // Pop “val” off the stack and store it
		      // in local variable 1.
IL_0004:  ldloc.1     // Put “val” back on the stack.
IL_0005:  ldc.i4.1    // Load the integer value 1 onto the stack.
IL_0006:  sub         // Subtract 1 from val and push the result
                      // onto the stack.

// Switch on the result.
IL_0007:  switch      (IL_0019, IL_0024, IL_002F)
IL_0018:  ret
IL_0019:  ldstr       "One"
IL_001E:  call        System.Console.WriteLine
IL_0023:  ret
IL_0024:  ldstr       "Two"
IL_0029:  call        System.Console.WriteLine
IL_002E:  ret
IL_002F:  ldstr       "Three"
IL_0034:  call        System.Console.WriteLine
{% endhighlight %}

As we can see, calculating the branch table index in this case is extremely simple. We simply subtract 1 from the value passed to the switch statement. 1 redirects to branch table index 0 which points to line IL_0019, 2 redirects to branch table index 1 which points to line IL_0024 and 3 redirects to branch table index 2 which points to line IL_002F. If by chance the branch table index falls outside the range of the branch table, program flow continues as normal and no redirection takes place.

## Slightly more complex cases

What we’ve looked at here is how a textbook switch/case statement is compiled and executed. I deliberately chose an example using values 1, 2 and 3 because the calculation to obtain the branch table index was simple and no extra compiler optimisations were included in the code. (If you want to see what I mean, look at the IL for a switch on 0, 1 and 2.) But as you would expect, the C# compiler is very smart indeed, and is able to handle scenarios that are more complex than this one. Take for example a set of non-contiguous values that aren’t too far apart from one-another. The compiler responds by adding additional cases to fill in the blanks, as we can see here.

{% highlight csharp %}
switch (val)
{
	case 1:
		Console.WriteLine("One");
		break;
	case 2:
		Console.WriteLine("Two");
		break;
	case 5:
		Console.WriteLine("Five");
		break;
}
{% endhighlight %}

{% highlight text %}
IL_0002:  ldloc.0     // val
IL_0003:  stloc.1     // CS$0$0000
IL_0004:  ldloc.1     // CS$0$0000
IL_0005:  ldc.i4.1
IL_0006:  sub
IL_0007:  switch      (IL_0021, IL_002C, IL_0041, IL_0041, IL_0037)
IL_0020:  ret
IL_0021:  ldstr       "One"
IL_0026:  call        System.Console.WriteLine
IL_002B:  ret
IL_002C:  ldstr       "Two"
IL_0031:  call        System.Console.WriteLine
IL_0036:  ret
IL_0037:  ldstr       "Five"
IL_003C:  call        System.Console.WriteLine
{% endhighlight %}

Notice how cases 3 and 4 simply redirect to the end of the program?

If the cases are too spread-apart then the compiler can choose a hybrid switch/if solution like so.

{% highlight csharp %}
switch (val)
{
	case 1:
		Console.WriteLine("One");
		break;
	case 2:
		Console.WriteLine("Two");
		break;
	case 500:
		Console.WriteLine("Five hundred.");
		break;
}
{% endhighlight %}

{% highlight text %}
IL_0002:  ldloc.0     // val
IL_0003:  stloc.1     // CS$0$0000
IL_0004:  ldloc.1     // CS$0$0000
IL_0005:  ldc.i4.1
IL_0006:  sub
IL_0007:  switch      (IL_001D, IL_0028)
IL_0014:  ldloc.1     // CS$0$0000
IL_0015:  ldc.i4      F4 01 00 00 // (500)
IL_001A:  beq.s       IL_0033
IL_001C:  ret
IL_001D:  ldstr       "One"
IL_0022:  call        System.Console.WriteLine
IL_0027:  ret
IL_0028:  ldstr       "Two"
IL_002D:  call        System.Console.WriteLine
IL_0032:  ret
IL_0033:  ldstr       "Five hundred."
IL_0038:  call        System.Console.WriteLine
{% endhighlight %}

Here the switch is performed on values 1 and 2. If neither of those cases are true, then it performs a test of equality to see if the value equals 500.

And finally, if the cases are wildly different from one another, the compiler can eschew the switch altogether and go for something functionally-similar to an if/else scenario as follows.

{% highlight csharp %}
switch (val)
{
	case 1:
		Console.WriteLine("One");
		break;
	case 29:
		Console.WriteLine("Twenty-nine");
		break;
	case 500:
		Console.WriteLine("Five hundred.");
		break;
}
{% endhighlight %}

{% highlight text %}
IL_0002:  ldloc.0     // val
IL_0003:  stloc.1     // CS$0$0000
IL_0004:  ldloc.1     // CS$0$0000
IL_0005:  ldc.i4.1
IL_0006:  beq.s       IL_0016
IL_0008:  ldloc.1     // CS$0$0000
IL_0009:  ldc.i4.s    1D
IL_000B:  beq.s       IL_0021
IL_000D:  ldloc.1     // CS$0$0000
IL_000E:  ldc.i4      F4 01 00 00
IL_0013:  beq.s       IL_002C
IL_0015:  ret
IL_0016:  ldstr       "One"
IL_001B:  call        System.Console.WriteLine
IL_0020:  ret
IL_0021:  ldstr       "Twenty-nine"
IL_0026:  call        System.Console.WriteLine
IL_002B:  ret
IL_002C:  ldstr       "Five hundred."
IL_0031:  call        System.Console.WriteLine
{% endhighlight %}

As you should see by now, the C# compiler exhibits a high level of intelligence and flexibility in determining how to treat each individual scenario. I had a lot of fun playing with all sorts of different cases and seeing how the compiler would react while writing this article and would encourage anyone interested to do the same. 

