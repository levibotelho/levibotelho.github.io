---
layout: post
status: publish
published: true
title: What is the difference between if and switch?
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "Although switch and if may seem to be two nearly-equivalent representations
  of the same concept, under the hood they function in two very different ways. Understanding
  the differences between the two makes for good general knowledge and provides one
  with an interesting glimpse at how the C# compiler works to optimise your code without
  you even knowing it.\r\n \r\n<h2>If/else statements</h2>\r\nIf/else statements at
  the IL level work pretty much as one would expect them to. The runtime evaluates
  each expression in a series until it finds one which is true, at which it jumps
  to and executes the code corresponding to the case. To illustrate, let’s look at
  the IL for the following code.\r\n\r\n[csharp]\r\nif (val == 1)\r\n\tConsole.WriteLine(&quot;One&quot;);\r\nelse
  if (val == 2)\r\n\tConsole.WriteLine(&quot;Two&quot;);\r\nelse if (val == 3)\r\n\tConsole.WriteLine(&quot;Three&quot;);\r\n[/csharp]\r\n\r\n[sourcecode]\r\nIL_000E:
  \ ldloc.0     // Load our “val” value onto the stack\r\nIL_000F:  ldc.i4.1    //
  Load the numeric value 1 onto the stack\r\nIL_0010:  bne.un.s    IL_001D    // Go
  to line IL_001D if the two\r\n                                 // values on the
  stack are NOT equal,\r\n                                 // otherwise execute the
  following...\r\nIL_0012:  ldstr       &quot;One&quot;\r\nIL_0017:  call        System.Console.WriteLine\r\nIL_001C:
  \ ret         \r\n// The remaining code simply repeats the above pattern...\r\nIL_001D:
  \ ldloc.0     // val\r\nIL_001E:  ldc.i4.2    \r\nIL_001F:  bne.un.s    IL_002C\r\nIL_0021:
  \ ldstr       &quot;Two&quot;\r\nIL_0026:  call        System.Console.WriteLine\r\nIL_002B:
  \ ret         \r\nIL_002C:  ldloc.0     // val\r\nIL_002D:  ldc.i4.3    \r\nIL_002E:
  \ bne.un.s    IL_003A\r\nIL_0030:  ldstr       &quot;Three&quot;\r\nIL_0035:  call
  \       System.Console.WriteLine\r\n[/sourcecode]\r\n\r\nNothing much to say about
  this really, this is pretty simple stuff. That said, it’s important to see how if
  statements function so that we have something to compare switch to. Let’s go ahead
  and take a look at how a switch statement accomplishes the same task.\r\n\r\n<h2>Switch
  statements</h2>\r\n\r\nLet’s dive right in by rewriting the above example as a switch
  statement and examining the corresponding IL.\r\n\r\n[csharp]\r\nswitch (val)\r\n{\r\n\tcase
  1:\r\n\t\tConsole.WriteLine(&quot;One&quot;);\r\n\t\tbreak;\r\n\tcase 2:\r\n\t\tConsole.WriteLine(&quot;Two&quot;);\r\n\t\tbreak;\r\n\tcase
  3:\r\n\t\tConsole.WriteLine(&quot;Three&quot;);\r\n\t\tbreak;\r\n}\r\n[/csharp]\r\n\r\n[sourcecode]\r\nIL_0002:
  \ ldloc.0     // Load our “val” value onto the stack\r\nIL_0003:  stloc.1     //
  CS$0$0000\r\nIL_0004:  ldloc.1     // CS$0$0000\r\nIL_0005:  ldc.i4.1    \r\nIL_0006:
  \ sub         \r\nIL_0007:  switch      (IL_0019, IL_0024, IL_002F)\r\nIL_0018:
  \ ret         \r\nIL_0019:  ldstr       &quot;One&quot;\r\nIL_001E:  call        System.Console.WriteLine\r\nIL_0023:
  \ ret         \r\nIL_0024:  ldstr       &quot;Two&quot;\r\nIL_0029:  call        System.Console.WriteLine\r\nIL_002E:
  \ ret         \r\nIL_002F:  ldstr       &quot;Three&quot;\r\nIL_0034:  call        System.Console.WriteLine\r\n[/sourcecode]\r\n\r\nThere
  are two really noticeable differences here in comparison to the switch. First, there
  is the presence of the “switch” right there in the middle of the code. And second,
  there are no equality, inequality, greater than or less than comparisons to be seen.
  So if switch doesn’t work by comparing sets of values, how does it correctly manage
  to route program flow?"
wordpress_id: 2532
wordpress_url: http://www.levibotelho.com/?p=2532
date: !binary |-
  MjAxMy0xMC0xNiAyMjo0ODoyOCArMDIwMA==
date_gmt: !binary |-
  MjAxMy0xMC0xNiAyMDo0ODoyOCArMDIwMA==
categories:
- C#
- CLR
tags:
- c#
- clr
- performance
comments: []
---
<p>Although switch and if may seem to be two nearly-equivalent representations of the same concept, under the hood they function in two very different ways. Understanding the differences between the two makes for good general knowledge and provides one with an interesting glimpse at how the C# compiler works to optimise your code without you even knowing it.</p>
<h2>If/else statements</h2>
<p>If/else statements at the IL level work pretty much as one would expect them to. The runtime evaluates each expression in a series until it finds one which is true, at which it jumps to and executes the code corresponding to the case. To illustrate, let’s look at the IL for the following code.</p>
<p>[csharp]<br />
if (val == 1)<br />
	Console.WriteLine(&quot;One&quot;);<br />
else if (val == 2)<br />
	Console.WriteLine(&quot;Two&quot;);<br />
else if (val == 3)<br />
	Console.WriteLine(&quot;Three&quot;);<br />
[/csharp]</p>
<p>[sourcecode]<br />
IL_000E:  ldloc.0     // Load our “val” value onto the stack<br />
IL_000F:  ldc.i4.1    // Load the numeric value 1 onto the stack<br />
IL_0010:  bne.un.s    IL_001D    // Go to line IL_001D if the two<br />
                                 // values on the stack are NOT equal,<br />
                                 // otherwise execute the following...<br />
IL_0012:  ldstr       &quot;One&quot;<br />
IL_0017:  call        System.Console.WriteLine<br />
IL_001C:  ret<br />
// The remaining code simply repeats the above pattern...<br />
IL_001D:  ldloc.0     // val<br />
IL_001E:  ldc.i4.2<br />
IL_001F:  bne.un.s    IL_002C<br />
IL_0021:  ldstr       &quot;Two&quot;<br />
IL_0026:  call        System.Console.WriteLine<br />
IL_002B:  ret<br />
IL_002C:  ldloc.0     // val<br />
IL_002D:  ldc.i4.3<br />
IL_002E:  bne.un.s    IL_003A<br />
IL_0030:  ldstr       &quot;Three&quot;<br />
IL_0035:  call        System.Console.WriteLine<br />
[/sourcecode]</p>
<p>Nothing much to say about this really, this is pretty simple stuff. That said, it’s important to see how if statements function so that we have something to compare switch to. Let’s go ahead and take a look at how a switch statement accomplishes the same task.</p>
<h2>Switch statements</h2>
<p>Let’s dive right in by rewriting the above example as a switch statement and examining the corresponding IL.</p>
<p>[csharp]<br />
switch (val)<br />
{<br />
	case 1:<br />
		Console.WriteLine(&quot;One&quot;);<br />
		break;<br />
	case 2:<br />
		Console.WriteLine(&quot;Two&quot;);<br />
		break;<br />
	case 3:<br />
		Console.WriteLine(&quot;Three&quot;);<br />
		break;<br />
}<br />
[/csharp]</p>
<p>[sourcecode]<br />
IL_0002:  ldloc.0     // Load our “val” value onto the stack<br />
IL_0003:  stloc.1     // CS$0$0000<br />
IL_0004:  ldloc.1     // CS$0$0000<br />
IL_0005:  ldc.i4.1<br />
IL_0006:  sub<br />
IL_0007:  switch      (IL_0019, IL_0024, IL_002F)<br />
IL_0018:  ret<br />
IL_0019:  ldstr       &quot;One&quot;<br />
IL_001E:  call        System.Console.WriteLine<br />
IL_0023:  ret<br />
IL_0024:  ldstr       &quot;Two&quot;<br />
IL_0029:  call        System.Console.WriteLine<br />
IL_002E:  ret<br />
IL_002F:  ldstr       &quot;Three&quot;<br />
IL_0034:  call        System.Console.WriteLine<br />
[/sourcecode]</p>
<p>There are two really noticeable differences here in comparison to the switch. First, there is the presence of the “switch” right there in the middle of the code. And second, there are no equality, inequality, greater than or less than comparisons to be seen. So if switch doesn’t work by comparing sets of values, how does it correctly manage to route program flow?<a id="more"></a><a id="more-2532"></a></p>
<p>Switch does this by implementing what is known as a branch table. You can think of a branch table as an array of locations in the program to which to navigate. These locations, known formally as “targets”, are what we see in parentheses beside the switch statement in the above IL. Switch selects the target to direct the program flow to by using a zero-based index which it simply pops off the stack. The genius of switch, however, is that the index is mathematically calculated from the value passed to the switch statement in C#, and not derived by evaluating a series of “if equals” statements. This allows switch to be consistently fast no matter how many cases it may contain. Where if may need to evaluate every single case in a long list in order to find the case that turns out to be true, switch simply calculates the branch table index and forges on. What is really neat about all this is that the calculation to determine the branch table index is visible directly in the IL of C# program. Let’s take another look at the above IL and see how it’s done in this case.</p>
<p>[sourcecode]<br />
IL_0002:  ldloc.0     // Load our “val” value onto the stack<br />
IL_0003:  stloc.1     // Pop “val” off the stack and store it<br />
		      // in local variable 1.<br />
IL_0004:  ldloc.1     // Put “val” back on the stack.<br />
IL_0005:  ldc.i4.1    // Load the integer value 1 onto the stack.<br />
IL_0006:  sub         // Subtract 1 from val and push the result<br />
                      // onto the stack.</p>
<p>// Switch on the result.<br />
IL_0007:  switch      (IL_0019, IL_0024, IL_002F)<br />
IL_0018:  ret<br />
IL_0019:  ldstr       &quot;One&quot;<br />
IL_001E:  call        System.Console.WriteLine<br />
IL_0023:  ret<br />
IL_0024:  ldstr       &quot;Two&quot;<br />
IL_0029:  call        System.Console.WriteLine<br />
IL_002E:  ret<br />
IL_002F:  ldstr       &quot;Three&quot;<br />
IL_0034:  call        System.Console.WriteLine<br />
[/sourcecode]</p>
<p>As we can see, calculating the branch table index in this case is extremely simple. We simply subtract 1 from the value passed to the switch statement. 1 redirects to branch table index 0 which points to line IL_0019, 2 redirects to branch table index 1 which points to line IL_0024 and 3 redirects to branch table index 2 which points to line IL_002F. If by chance the branch table index falls outside the range of the branch table, program flow continues as normal and no redirection takes place.</p>
<h2>Slightly more complex cases</h2>
<p>What we’ve looked at here is how a textbook switch/case statement is compiled and executed. I deliberately chose an example using values 1, 2 and 3 because the calculation to obtain the branch table index was simple and no extra compiler optimisations were included in the code. (If you want to see what I mean, look at the IL for a switch on 0, 1 and 2.) But as you would expect, the C# compiler is very smart indeed, and is able to handle scenarios that are more complex than this one. Take for example a set of non-contiguous values that aren’t too far apart from one-another. The compiler responds by adding additional cases to fill in the blanks, as we can see here.</p>
<p>[csharp]<br />
switch (val)<br />
{<br />
	case 1:<br />
		Console.WriteLine(&quot;One&quot;);<br />
		break;<br />
	case 2:<br />
		Console.WriteLine(&quot;Two&quot;);<br />
		break;<br />
	case 5:<br />
		Console.WriteLine(&quot;Five&quot;);<br />
		break;<br />
}<br />
[/csharp]</p>
<p>[sourcecode]<br />
IL_0002:  ldloc.0     // val<br />
IL_0003:  stloc.1     // CS$0$0000<br />
IL_0004:  ldloc.1     // CS$0$0000<br />
IL_0005:  ldc.i4.1<br />
IL_0006:  sub<br />
IL_0007:  switch      (IL_0021, IL_002C, IL_0041, IL_0041, IL_0037)<br />
IL_0020:  ret<br />
IL_0021:  ldstr       &quot;One&quot;<br />
IL_0026:  call        System.Console.WriteLine<br />
IL_002B:  ret<br />
IL_002C:  ldstr       &quot;Two&quot;<br />
IL_0031:  call        System.Console.WriteLine<br />
IL_0036:  ret<br />
IL_0037:  ldstr       &quot;Five&quot;<br />
IL_003C:  call        System.Console.WriteLine<br />
[/sourcecode]</p>
<p>Notice how cases 3 and 4 simply redirect to the end of the program?</p>
<p>If the cases are too spread-apart then the compiler can choose a hybrid switch/if solution like so.</p>
<p>[csharp]<br />
switch (val)<br />
{<br />
	case 1:<br />
		Console.WriteLine(&quot;One&quot;);<br />
		break;<br />
	case 2:<br />
		Console.WriteLine(&quot;Two&quot;);<br />
		break;<br />
	case 500:<br />
		Console.WriteLine(&quot;Five hundred.&quot;);<br />
		break;<br />
}<br />
[/csharp]</p>
<p>[sourcecode]<br />
IL_0002:  ldloc.0     // val<br />
IL_0003:  stloc.1     // CS$0$0000<br />
IL_0004:  ldloc.1     // CS$0$0000<br />
IL_0005:  ldc.i4.1<br />
IL_0006:  sub<br />
IL_0007:  switch      (IL_001D, IL_0028)<br />
IL_0014:  ldloc.1     // CS$0$0000<br />
IL_0015:  ldc.i4      F4 01 00 00 // (500)<br />
IL_001A:  beq.s       IL_0033<br />
IL_001C:  ret<br />
IL_001D:  ldstr       &quot;One&quot;<br />
IL_0022:  call        System.Console.WriteLine<br />
IL_0027:  ret<br />
IL_0028:  ldstr       &quot;Two&quot;<br />
IL_002D:  call        System.Console.WriteLine<br />
IL_0032:  ret<br />
IL_0033:  ldstr       &quot;Five hundred.&quot;<br />
IL_0038:  call        System.Console.WriteLine<br />
[/sourcecode]</p>
<p>Here the switch is performed on values 1 and 2. If neither of those cases are true, then it performs a test of equality to see if the value equals 500.</p>
<p>And finally, if the cases are wildly different from one another, the compiler can eschew the switch altogether and go for something functionally-similar to an if/else scenario as follows.</p>
<p>[csharp]<br />
switch (val)<br />
{<br />
	case 1:<br />
		Console.WriteLine(&quot;One&quot;);<br />
		break;<br />
	case 29:<br />
		Console.WriteLine(&quot;Twenty-nine&quot;);<br />
		break;<br />
	case 500:<br />
		Console.WriteLine(&quot;Five hundred.&quot;);<br />
		break;<br />
}<br />
[/csharp]</p>
<p>[sourcecode]<br />
IL_0002:  ldloc.0     // val<br />
IL_0003:  stloc.1     // CS$0$0000<br />
IL_0004:  ldloc.1     // CS$0$0000<br />
IL_0005:  ldc.i4.1<br />
IL_0006:  beq.s       IL_0016<br />
IL_0008:  ldloc.1     // CS$0$0000<br />
IL_0009:  ldc.i4.s    1D<br />
IL_000B:  beq.s       IL_0021<br />
IL_000D:  ldloc.1     // CS$0$0000<br />
IL_000E:  ldc.i4      F4 01 00 00<br />
IL_0013:  beq.s       IL_002C<br />
IL_0015:  ret<br />
IL_0016:  ldstr       &quot;One&quot;<br />
IL_001B:  call        System.Console.WriteLine<br />
IL_0020:  ret<br />
IL_0021:  ldstr       &quot;Twenty-nine&quot;<br />
IL_0026:  call        System.Console.WriteLine<br />
IL_002B:  ret<br />
IL_002C:  ldstr       &quot;Five hundred.&quot;<br />
IL_0031:  call        System.Console.WriteLine<br />
[/sourcecode]</p>
<p>As you should see by now, the C# compiler exhibits a high level of intelligence and flexibility in determining how to treat each individual scenario. I had a lot of fun playing with all sorts of different cases and seeing how the compiler would react while writing this article and would encourage anyone interested to do the same. </p>
