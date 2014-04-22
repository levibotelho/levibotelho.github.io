---
layout: post
status: publish
published: true
title: ! '[C#] Compare generic variables for equality'
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
wordpress_id: 3552
wordpress_url: http://www.levibotelho.com/?p=3552
date: !binary |-
  MjAxNC0wMS0xMyAyMjoyMjowNCArMDEwMA==
date_gmt: !binary |-
  MjAxNC0wMS0xMyAyMToyMjowNCArMDEwMA==
categories:
- C#
tags:
- c#
- generics
comments: []
---
<p>This is a simple task, yet one which is frequently carried out incorrectly. This kind of comparison is often required when dealing with classes that look like the following.</p>
<p>[csharp]<br />
class GenericContainer&lt;T&gt;<br />
{<br />
    T _variable;</p>
<p>    public T Variable<br />
    {<br />
        get { return _variable; }<br />
        set<br />
        {<br />
            // Test for equality before setting.</p>
<p>            _variable = value;<br />
            // Invoke an event here...<br />
        }<br />
    }<br />
}<br />
[/csharp]</p>
<p>Now the knee-jerk reaction to compare <code>_variable</code> and <code>value</code> in this case is to do this.</p>
<p>[csharp]<br />
set<br />
{<br />
    if (_variable == value)<br />
        return;</p>
<p>    _variable = value;<br />
    // Monkey business...<br />
}<br />
[/csharp]</p>
<p>However, this doesn’t compile. This makes sense, because we cannot guarantee that the type <code>T</code> overloads the equality operator (reference types do by default, but value types do not). In light of this problem, many people will resort to the following.</p>
<p>[csharp]<br />
set<br />
{<br />
    if (_variable.Equals(value))<br />
        return;</p>
<p>    _variable = value;<br />
    // Monkey business...<br />
}<br />
[/csharp]</p>
<p>However this poses a different problem. If <code>_variable</code> is ever null, your program will throw a null reference exception. But there is a way around this. Simply use the static implementation of <code>Equals()</code> on <code>System.Object</code> as follows.</p>
<p>[csharp]<br />
set<br />
{<br />
    // We don’t even need to write object.Equals<br />
    // because we are currently on an object.<br />
    if (Equals(_variable, value))<br />
        return;</p>
<p>    _variable = value;<br />
    // Monkey business.<br />
}<br />
[/csharp]</p>
<p>Problem solved! The one thing to keep in mind with this technique is that <code>Object.Equals()</code> compares reference types for reference equality, and value types for value equality (corresponding fields on both variables are compared). If you have implemented some form of custom equality logic (normally by way of <code>IEquatable</code>) then you will need to specifically invoke the interface method and limit your generic type argument to <code>IEquatable</code> implementations.</p>
