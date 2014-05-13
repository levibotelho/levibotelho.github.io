---
layout: post
title: ! '[C#] Async processes with TaskCompletionSource'
category: Development
tags: [c#, async]
comments: true
share: true
---

If you've ever tried to launch a new process in C#, you'll probably have noticed that the `Process` object lacks an asynchronous API. Luckily though, there is an easy way to write an asynchronous wrapper for a process such that you can launch it and `await` its termination.

## Getting to know Process

The first step in writing the wrapper is seeing how `Process` works under normal circumstances. If you are writing code which spawns a process and writes to the console window when it finishes, you might do so as follows.

{% highlight csharp %}
var process = new Process
{
    EnableRaisingEvents = true,
    StartInfo = new ProcessStartInfo("application.exe")
    {
        RedirectStandardError = true,
        UseShellExecute = false
    }
};

process.Exited += (sender, args) =>
{
    if (process.ExitCode != 0)
    {
        var errorMessage = process.StandardError.ReadToEnd();
        throw new InvalidOperationException(errorMessage);
    }
    Console.WriteLine("The process has exited.");
    process.Dispose();
};

_process.Start();
{% endhighlight %}

This code works, but it's not terribly convenient to use. Let's look at how we can make it `async` compatible.

## TaskCompletionSource basics

The key to our quest for asynchronicity is `TaskCompletionSource`. `TaskCompletionSource` is a class which wraps a `Task` whose state we can manually control. This is more easily understood with an example. Take a look at this console application.

{% highlight csharp %}
static void Main(string[] args)
{
    var task = DoWork();
    Console.WriteLine("DoWork called");
    // We call .Result because Main cannot be async
    Console.WriteLine(task.Result);
    Console.ReadLine();
}

static Task<int> DoWork()
{
    var tcs = new TaskCompletionSource<int>();
    Task.Run(async () =>
    {
        await Task.Delay(1000);
        tcs.SetResult(6);
    });
    return tcs.Task;
}
{% endhighlight %}

When run, this code outputs "DoWork called", pauses for approximately one second, and then outputs "6". This behaviour is all due to the `TaskCompletionSource` object that is created in the `DoWork` method.

When a `TaskCompletionSource` is first instantiated, the status of its underlying `Task` object is set to `WaitingForActivation`. In this state, any code which awaits the task will block. However, when `SetResult` is called, the status of the task changes to `Completed` and the task's `Result` property is set to the value passed to the `SetResult` call. This causes threads waiting on the task to unblock and program execution to resume. In our example, the call to `task.Result` in `Main` blocks until `tcs.SetResult(6)` is called by the task launched in `DoWork`.

## Calling a Process asynchronously

So, now that we've seen how a `TaskCompletionSource` can be used to create a manually-controlled asynchronous method, we can apply the same logic to create an awaitable method that abstracts the behaviour of a `Process`.

{% highlight csharp %}
public static Task RunProcessAsync(string processPath)
{
    var tcs = new TaskCompletionSource<object>();
    var process = new Process
    {
        EnableRaisingEvents = true,
        StartInfo = new ProcessStartInfo(processPath)
        {
            RedirectStandardError = true,
            UseShellExecute = false
        }
    };
    process.Exited += (sender, args) =>
    {
        if (process.ExitCode != 0)
        {
            var errorMessage = process.StandardError.ReadToEnd();
            throw new InvalidOperationException("The process did not exit correctly. " +
                "The corresponding error message was: " + errorMessage);
        }
        tcs.SetResult(null);
        process.Dispose();
    };
    process.Start();
    return tcs.Task;
}
{% endhighlight %}

This method looks a lot like what we saw earlier. When called, it instantiates a new `TaskCompletionSource<object>` and sets its result to `null` when the process's `Exited` event is fired. Using this method, we can create a process and await its completion like so.

{% highlight csharp %}
await RunProcessAsync("myexecutable.exe");
{% endhighlight %}

Needless to say, if we were passing anything meaningful to `tcs.SetResult`, such as a value obtained from standard output, we could retrieve the value with a simple variable assignment.

{% highlight csharp %}
var result = await RunProcessAsync("myexecutable.exe");
{% endhighlight %}

Pretty cool if you ask me.

You may be wondering why we use a `TaskCompletionSource<object>` and not just a `TaskCompletionSource`. The reason is simply because a non-generic `TaskCompletionSource` does not exist. Because in this case we have no return value to assign, we have simply opted to use a `TaskCompletionSource<object>` and to set its result to `null` upon completion.