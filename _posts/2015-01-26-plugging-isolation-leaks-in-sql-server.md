---
layout: post
title: Plugging isolation level leaks in SQL Server
category: Development
tags: [sql-server, c#, ado.net]
description: The transaction isolation level set on your SQL Server connections may not be what you think it is...
comments: true
share: true
---
Transaction isolation levels are a critical concept to understand when working with SQL. As a quick refersher, the isolation level of a transaction defines how the database handles locking and concurrency. A lower level provides better performance at the expense of data integrity, and a higher level provides better data integrity at the expense of performance. Using the wrong isolation level can lead to corrupted data and/or deadlocking.

Given how important this stuff is, it might surprise you to know that the isolation level set on your application's SQL Server connections may not be what you think it is. If you are using SQL Server 2012 or older and have code that looks like this, you'll want to read on.

{% highlight csharp %}
var op = new TransactionOptions
{
	IsolationLevel = /* An isolation level that is not ReadCommitted */
};
using (var tx = new TransactionScope(TransactionScopeOption.RequiresNew, op))
{
    var con = new SqlConnection("...");
    var com = new SqlCommand("...", con);
    con.Open();
    // ...
    con.Close();
    tx.Complete();
}
{% endhighlight %}

# Here's why

Outside of stored procedures and triggers, when you set a transaction isolation level in SQL Server it is set for  the entire connection, and not just the next transaction. This means that you have to remember to set it back to its default value when you're done, like so.

{% highlight sql %}
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
GO
BEGIN TRANSACTION;
GO
-- Do some mission-critical stuff here.
GO
COMMIT TRANSACTION;
GO
SET TRANSACTION ISOLATION LEVEL READ COMMITTED
GO
{% endhighlight %}

Now, one might assume that in the C# code above the isolation level of the connection is reset when it is returned to the connection pool at the end of the `using` statement. As logical as this may sound, if you're using a version of SQL Server prior to SQL Server 2014, *this is not the case*.

The reason for this is that when a connection is returned to the pool, `sp_reset_connection` is called, which resets it to a default state. In versions of SQL Server before SQL Server 2014, `sp_reset_connection` does not reset the transaction isolation level! This means that if an isolation level is set on a connection, the next time that connection is pulled from the pool it will retain the isolation level from its previous usage.

Off the top of my head I can think of two big ways that this can harm your application.

1. A user of your application calls a method which uses a transaction with the isolation level set to `READ UNCOMMITTED`. This level is commonly used to improve performance when retrieving large amounts of data that doesn't have to be 100% accurate (such as data used to generate statistics). The connection returns to the pool and the next time it is used incorrect data is returned from the database due to insufficient locking when reading.
2. A user of your application calls a method which uses a transaction with the isoloation level set to `SERIALIZABLE`. This level employes aggressive locking techniques to ensure strict data integrity for the lifetime of the transaction. The connection returns to the pool, and the next time it is used a deadlock occurs due to the database being too aggressive when locking data for reading.

What I find incredible about all of this is that the default behaviour of the above code launches you into a game of Russian roulette, where at some nondeterministic point in time you have a good chance of damaging your application. This isolation level "leaking" is not very well documented, and the information I found on it came primarily from a few [Stack Overflow questions](http://stackoverflow.com/q/2425550/1068266) and a [Microsoft Support article*](http://support2.microsoft.com/?id=972915).

# Stop the madness

There are a few ways to keep your application safe from all this. The most obvious is to simply use SQL Server 2014. If this isn't possible, the Microsoft Support article referenced above recommends doing one of the following:

- Specify the transaction isolation level explicitly.
- Use the `TransactionOption` property if you use the `TransactionScope` class.
- Specify the `IsolationLevel` enum value if you use the `SqlTransaction` class.
- Execute the `SET TRANSACTION ISOLATION LEVEL` statement by using the `ExecuteNonQuery` method if you do not open an explicit transaction.

No matter which of these methods you decide to use, you should be employing it in a common location in your code base that ensures that every connection that your application uses has had its isolation level reset to the default value before it is consumed.

---

*\* The Microsoft Support article contains code that you can run to replicate the error, just in case this all seems to crazy to be true. The easiest way to see the difference if you have SQL Server LocalDB installed on your machine is to define two connection strings, one for LocalDB V11.0 and one for LocalDB V12.0 and compare the two results.* 