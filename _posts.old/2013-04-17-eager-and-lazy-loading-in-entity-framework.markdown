---
layout: post
status: publish
published: true
title: Eager and Lazy Loading in Entity Framework
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "If you have a customer entity that looks like this…\r\n\r\n[sourcecode
  language=\"csharp\"]\r\npublic class Customer\r\n{\r\n    public int Id { get; set;
  }\r\n    public string Name { get; set; }\r\n    public string Address { get; set;
  }\r\n    public string PhoneNumber { get; set; }\r\n}\r\n[/sourcecode]\r\n\r\n…and
  need to get the customer’s name, chances are you will load the entire entity from
  the database and merely disregard the excess data. The simplicity of this is wonderful,
  and the impact that the excess data retrieval and storage has on application performance
  is trivial. Such may not be the case however if the customer entity looks like this:\r\n\r\n[sourcecode
  language=\"csharp\"]\r\npublic class Customer\r\n{\r\n    public int Id { get; set;
  }\r\n    public string Name { get; set; }\r\n    public string Address { get; set;
  }\r\n    public string PhoneNumber { get; set; }\r\n    public ICollection&lt;Purchase&gt;
  Purchases { get; set; }\r\n}\r\n[/sourcecode]\r\n"
wordpress_id: 152
wordpress_url: http://levibotelho.azurewebsites.net/?p=152
date: !binary |-
  MjAxMy0wNC0xNyAxMDozNToxMyArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODozNToxMyArMDIwMA==
categories:
- Database
tags:
- c#
- entity framework
- performance
comments:
- id: 1142
  author: Mathieu
  author_email: xmamat@hotmail.com
  author_url: ''
  date: !binary |-
    MjAxMy0wNS0yNyAxMzo0MDoxNyArMDIwMA==
  date_gmt: !binary |-
    MjAxMy0wNS0yNyAxMTo0MDoxNyArMDIwMA==
  content: Clear and concise as usual. Thanks!
- id: 28801
  author: srinivasu
  author_email: dandamudimca2010@gmail.com
  author_url: ''
  date: !binary |-
    MjAxNC0wMi0wNyAwOToyMToyNCArMDEwMA==
  date_gmt: !binary |-
    MjAxNC0wMi0wNyAwODoyMToyNCArMDEwMA==
  content: Thanks, Simple and clear.
---
<p>If you have a customer entity that looks like this…</p>
<p>[sourcecode language="csharp"]<br />
public class Customer<br />
{<br />
    public int Id { get; set; }<br />
    public string Name { get; set; }<br />
    public string Address { get; set; }<br />
    public string PhoneNumber { get; set; }<br />
}<br />
[/sourcecode]</p>
<p>…and need to get the customer’s name, chances are you will load the entire entity from the database and merely disregard the excess data. The simplicity of this is wonderful, and the impact that the excess data retrieval and storage has on application performance is trivial. Such may not be the case however if the customer entity looks like this:</p>
<p>[sourcecode language="csharp"]<br />
public class Customer<br />
{<br />
    public int Id { get; set; }<br />
    public string Name { get; set; }<br />
    public string Address { get; set; }<br />
    public string PhoneNumber { get; set; }<br />
    public ICollection&lt;Purchase&gt; Purchases { get; set; }<br />
}<br />
[/sourcecode]<br />
<a id="more"></a><a id="more-152"></a><br />
If the Purchases collection is rarely used during the lifetime of a given Customer object, the extra processing power and memory required to retrieve and store it may be wasteful. This cost is compounded if Purchase objects happen to be large. On the other hand, loading the collection along with the parent entity may be advantageous if it tends to play a key role in the use of a given Customer. Luckily, Entity Framework provides the developer with several different ways of managing the retrieval and storage of related entities. Two which are extremely useful to know of are called "lazy" and "explicit" loading.</p>
<h1>Lazy Loading</h1>
<p>Related entities that are “lazily” loaded are by default not loaded into the parent entity when the parent is queried from the data context. The property containing the related entity will remain present within the parent, but will only be filled with data if and when it is accessed.</p>
<p>To implement lazy loading for a property, simply make the property virtual like so:</p>
<p>[sourcecode language="csharp"]<br />
public class Customer<br />
{<br />
    public int Id { get; set; }<br />
    public string Name { get; set; }<br />
    public string Address { get; set; }<br />
    public string PhoneNumber { get; set; }<br />
    public virtual ICollection&lt;Purchase&gt; Purchases { get; set; }<br />
}<br />
[/sourcecode]</p>
<p>Removing the virtual keyword disables lazy loading. To disable it globally for an entire data context the LazyLoadingEnabled property on the context's Configuration object needs to be set to false in the context's constructor as so:</p>
<p>[sourcecode language="csharp"]<br />
public MyDataContext()<br />
    : base(&quot;Name=MyConnectionString&quot;)<br />
{<br />
    Configuration.LazyLoadingEnabled = false;<br />
}<br />
[/sourcecode]</p>
<h1>Eager Loading</h1>
<p>Eager loading is the opposite of lazy loading. While lazy loading waits until data is needed to query it from the database, eager loading loads it from the get-go at the same time as its parent. This guarantees that the data will be accessible without the need for a second database call.</p>
<p>To implement eager loading for a given property you use the include method in the query and pass it a lambda expression that points to the property that you wish to eagerly load, as follows:</p>
<p>[sourcecode language="csharp"]<br />
var customers = context.Customers<br />
    .Include(x =&gt; x.Purchases)<br />
    .ToList();<br />
[/sourcecode]</p>
<h1>When to Use What</h1>
<p>The proper loading method to employ in a given situation generally depends on the following two criteria:</p>
<ul>
<li>The probability that a given related entity will be accessed throughout the lifetime of a given entity.</li>
<li>The amount of processing power and memory required to load and maintain the related entity. This depends both on its size and the amount of effort required to retrieve it from the database.</li>
</ul>
<p>In general, the more expensive it is to load and maintain a related entity, the more often it must be used for eager loading to make sense. In my experience lazy loading tends to be the better fit in many cases, but the two options are equally valuable tools in optimising the performance of a given Entity Framework-based application.</p>
