[C#] Timers and garbage collection confusion

First and foremost, please excuse me for the ridiculous post title. I couldn't help myself ;).

Now then, down to business. Timers in .NET are one of those things that seem really simple and easy to understand up until the moment when you start asking some basic questions about how they work. My hope here is to, in as few words as possible, take a look at the different timer classes in .NET, what they are used for, and how they work. Let's get started.

## Too many timers

The .NET Framework defines five timer classes. These five timers can be grouped into two categories: UI timers, and "worker" timers.