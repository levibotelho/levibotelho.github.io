---
layout: post
title: Inlining table-valued functions in SQL Server
category: Database
tags: [sql server, performance]
description: Increase the performance of table-valued functions in SQL Server with inlining.
comments: true
share: true
---

Table valued functions in SQL Server are great for writing DRY SQL code by encapsulating commonly-used snippets of database logic. However, in some cases they can be at the root of serious performance problems. Let's take a look at an example of this. Say that we have a table that looks like the following.

{% highlight sql %}
CREATE TABLE Products
(
	ProductId INT,
	VendorId INT,
	Description NVARCHAR(1000)
)
{% endhighlight %}

Now imagine that in the context of a stored procedure we have the following recurring logic.

{% highlight sql %}
SELECT *
FROM Products
WHERE Description LIKE @Description
{% endhighlight %}

We may want to refactor this into a function to make our stored procedure more readable and easier to refactor. Many tutorials on the internet will tell you to create a table valued function as follows.

{% highlight sql %}
CREATE FUNCTION FN_Products_GetByDescription (@description NVARCHAR(1000))
RETURNS @results TABLE
(
	ProductId INT,
	VendorId INT,
	Description NVARCHAR(1000)
)
AS
BEGIN;
	INSERT INTO @results
	SELECT *
	FROM Products
	WHERE Description LIKE @description;
	RETURN;
END;
{% endhighlight %}

The problem with this type of function is that it acts as a black box for the code that calls it. Imagine that we call our new function in the following scenario. 

{% highlight sql %}
SELECT *
FROM FN_Products_GetByDescription('%book%')
WHERE VendorID = 3;
{% endhighlight %}

To execute this, SQL Server has to scan through every product in our database, checking the product description against our query text, before returning that result set to the calling code which then filters them on by VendorID. In large databases this is clearly very inefficient. Wouldn't it be better to let SQL Server know from the very beginning that we want to filter on our query text as well as the VendorID? That way, it could first filter by VendorID, and then execute the expensive LIKE filtering on a smaller result set. 
This is where function inlining comes into play. To inline our function, we simply rewrite it as follows.

{% highlight sql %}
CREATE FUNCTION FN_Products_GetByDescription2 (@description NVARCHAR(1000))
RETURNS TABLE
AS
RETURN
(
	SELECT *
	FROM Products
	WHERE Description LIKE @description
);
{% endhighlight %}

Now, when SQL Server executes the above query, the function is behind the scenes treated as a part of the calling code, instead of a black box that needs to return a value before going any further. This allows the query optimizer to be much more effective at optimizing queries, because it knows right from the start exactly what data we need.

So when would we ever **not** want to inline table valued functions? Well, functions can only be inlined when they consist of a single SELECT statement which returns a result set. Functions that contain multiple subqueries which fill a results table, as well as those which contain additional statements such as IFs aren't inlineable. However, if you can make a query inlineable, either directly or by rewriting it slightly, then it is generally a very good idea. The performance gains that you can get from function inlining can be dramatic, and afford you the flexibility and readability of DRY code, without a large performance cost.