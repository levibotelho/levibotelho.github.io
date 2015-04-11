---
layout: post
title: ! '[C#] The "?." operator in C# 6?'
category: Development
tags: [c#]
comments: true
share: true
redirect_from: "/c-the-operator-in-c-6/"
---
[Visual Studio User Voice](http://visualstudio.uservoice.com/) is a great site. It lets the community at large propose and vote for features in upcoming Microsoft products.

Some time ago I cast my vote for a feature which I felt has been lacking for a *long* time in C#: the `?.` operator.

`?.` would serve to eliminate chained null reference checks when accessing fields and properties on objects. The idea is that instead of writing this

`return obj != null ? obj.X : null;`

you could simply write this

`return obj?.X;`

Pretty useful eh? Especially when you are dealing with several layers of objects embedded in one another (I'm looking at you, `HttpContext`!).

Well I was absolutely elated to receive an email in my inbox this morning from Visual Studio User Voice regarding this issue. C# language PM Mads Torgersen posted on the `?.` idea page stating the following.

<hr />
We are seriously considering this feature for C# and VB, and will be prototyping it in coming months. Syntax in C# would be

<ul>
<li>`e?.x // member access`</li>
<li>`e?.M(…) // method invocation`</li>
<li>`e?[…] // indexing`</li>
</ul>

Semantically this will be similar to

`(e == null) ? null : e.x`

etc., except that e will only be evaluated once of course.

If the type of e.x (etc) is a non-nullable value type S, then the type of e?.x is S?. Otherwise the type of e?.x is the same as that of e.×. If we can’t tell whether the type is a non-nullable value type (because it is a type parameter without sufficient constraints) we’ll probably give a compile-time error.

<hr />
Let's hope it makes the final cut!

