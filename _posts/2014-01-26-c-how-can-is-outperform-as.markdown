---
layout: post
title: ! '[C#] How can is outperform as?'
category: C#
tags: [c#, clr, performance]
comments: true
share: true
---
Take a look at this code

{% highlight csharp %}
var sum = 0;
for (var i = 0; i < 100000000; i++)
{
	object val;
	if (i % 2 == 0)
		val = 1;
	else
		val = null;

	// either
	var intVal = val as int?;
	if (val != null)
		sum += intVal.Value;
	// or
	if (val is int)
		sum += (int)val;
}
{% endhighlight %}

Would you believe that the variant using `is` executes over six times faster than the variant that uses `as`? And before you accuse me of being unfair, the difference is not only due to the overhead involved in using a nullable integer. When dealing with checking the type compatibility of value types, `is` is genuinely faster than `as`. This runs counter to what many developers assume, which is that `as` is always the better choice due to the fact that you only validate type compatibility once.

To understand why this is the case, we first need to get to know two IL instructions.

## A quick IL introduction

### castclass

The castclass instruction serves to cast a variable to a specified type. If the cast succeeds, the casted value is returned to the stack unchanged, with the runtime now treating it as an instance of the new type. If the cast fails, an InvalidCastException is thrown.

### isinst

The isinst instruction serves to test whether a variable is castable to another type. If it is, the tested value is returned to the stack unchanged, with the runtime now treating it as an instance of the new type, just like castclass. If it isn’t, null is returned and no exception is thrown.

## Back to the problem

Let’s now look at the IL for the `is` and `as` variants to try and understand why `is` offers better performance.

### is

{% highlight text %}
// Local variable 2 holds “val” which is either null or a boxed int.
// “val” is on the top of the stack.
IL_0017:  isinst      System.Int32
IL_001C:  brfalse.s   IL_0027 // Loop if not an int
IL_001E:  ldloc.0     // Load sum
IL_001F:  ldloc.2     // Load val
IL_0020:  unbox.any   System.Int32 // Unbox the boxed int.
// Add the value and loop
{% endhighlight %}

### as

{% highlight text %}
// Local var 2 holds “val” which is either null or a boxed int?.
// “val” is on the top of the stack.
IL_0017:  isinst      System.Nullable<System.Int32>
IL_001C:  unbox.any   System.Nullable<System.Int32>
IL_0021:  stloc.3     // store the unboxed value (intVal)
IL_0022:  ldloca.s    03 // Load intVal
IL_0024:  call        System.Nullable<System.Int32>.get_HasValue
IL_0029:  brfalse.s   IL_0035 // Branch if intVal is null
IL_002B:  ldloc.0     // sum
IL_002C:  ldloca.s    03 // intVal
IL_002E:  call        System.Nullable<System.Int32>.get_Value
// Add the value and loop
{% endhighlight %}

### Breaking it down

In the case of the `is` statement, we start by verifying if val is an integer. If it is, we load sum onto the stack, reload val onto the stack and unbox it. We then add the unboxed val to sum and loop.

The code for `as` is a little more complicated. We start out by executing the isinst instruction on val to check if it can be treated as a nullable integer. The result of this call is assigned to the local variable intVal. Note that intVal is a boxed nullable integer which holds either val or null if val is not a valid integer. intval is now unboxed and HasValue is called to see whether or not it is null.

Notice the key difference in the `as` code. Unboxing is *always* performed on val when we are using `as`. This is because the result of the isinst instruction is a boxed nullable integer holding either a valid int or a null value that we need to unbox in order to call HasValue. When using <code>is` this unboxing is only performed if we know we have an integer on our hands.

It is this additional boxing that is responsible, along with the nullable overhead, for the performance loss incurred when using `as`. The cost of the unboxing combined with the cost of having to use a nullable value type greatly outweighs the small performance gain achieved by verifying type compatibility only once. If you modify the code such that both variants use a nullable integer and that the `if` statement always returns an integer (so that unboxing is required 100% of the time in both cases), you will see the performance difference disappear.