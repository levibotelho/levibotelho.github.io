---
layout: post
title: The danger of default method values in C#
category: [C#]
tags: [c#]
comments: true
share: true
---
While seemingly benign, methods containing default parameter values can be the cause of mysterious errors in deployed applications. To see why, let’s define a new class library called “ConsoleWriter” and add to it the following code.

{% highlight csharp %}
public static class ConsoleWriter
{
    public static void WriteMessage(string message = "Hello World")
    {
        Console.WriteLine(message);
    }
}
{% endhighlight %}

Pretty run-of-the-mill stuff. Let’s compile this DLL and reference it in a new solution which will call WriteMessage.

{% highlight csharp %}
static void Main(string[] args)
{
    ConsoleWriter.ConsoleWriter.WriteMessage();
    Console.ReadLine();
}
{% endhighlight %}

Now let's build the project and run the generated executable (in the bin folder). As expected, “Hello World” is printed to the console window. But now let’s go back to our ConsoleWriter project and change our default message from “Hello World” to “Goodbye World” and rebuild the DLL.
<a id="more"></a><a id="more-1792"></a>

{% highlight csharp %}
public static class ConsoleWriter
{
    public static void WriteMessage(string message = "Goodbye World")
    {
        Console.WriteLine(message);
    }
}
{% endhighlight %}

Now let’s re-execute the .exe we built earlier. Surprisingly, we see that when we run our executable “Hello World” is still printed to the console window!
To understand how this can be, we need to look at the IL for the Main method in our executable.

{% highlight text %}
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       17 (0x11)
  .maxstack  8
  IL_0000:  ldstr      "Hello World."
  IL_0005:  call       void [ConsoleWriter]ConsoleWriter.ConsoleWriter::WriteMessage(string)
  IL_000a:  call       string [mscorlib]System.Console::ReadLine()
  IL_000f:  pop
  IL_0010:  ret
} // end of method Program::Main
{% endhighlight %}

Notice how “Hello World” is embedded directly in the IL. *When calling a method in another module (as we are doing here) the default values for parameters are embedded in the calling code.* This means that if we go back and modify the method’s default value without recompiling the calling application, the value change will not be taken into account.* ** While this scenario is rarely encountered during the development phase of an application, it has the potential to occur frequently in production environments where it is common to upgrade a single DLL without redelivering an entire solution. The fact that you will only run into this issue in very rare cases during development makes it all the more dangerous, as relatively few developers become aware of the problem to begin with.
There is however, a simple way to avoid it. In our case, all we need to do to fix our WriteMessage code is by doing the following.

{% highlight csharp %}
public static void WriteMessage(string message = null)
{
    if (message == null)
        message = "Goodbye World.";

    Console.WriteLine(message);
}
{% endhighlight %}

Now, while null will still be embedded in calling modules as a default value, the real default value of the method (which in this case is “Goodbye World”) will not be. If we later want to change “Goodbye World” to something else, we can do so and redeliver our ConsoleWriter DLL without having to worry that a “Goodbye World” is still lurking somewhere in a calling module.

<hr />
** If you followed along with this demonstration but got lazy and threw both projects in a single solution, or launched the console application via the VS debugger, it would have been recompiled and you would not have arrived at the same result as I have here.*

** This might remind you of [a similar topic that I wrote about last month](http://www.levibotelho.com/the-difference-between-const-and-readonly-in-c/).

