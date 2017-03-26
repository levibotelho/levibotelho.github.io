---
layout: post
title: Primary keys and clustered indexes
category: Development
tags: [sql-server]
description: Explains the difference between primary keys and clustered indexes in SQL Server.
---
Primary keys and clustered indexes are two distinct concepts that often get mixed up with one another, but it’s important to understand the difference between them in order to build robust, performant SQL databases.

## What is a primary key?

A primary key is a column or collection of columns that uniquely identifies a database record.

## What is a clustered index?

Database indexes are b-tree data structures that are sorted based on a given set of fields. When a SQL query uses these fields to filter a set of records, SQL Server can run through the b-tree to find matching records quickly and efficiently. In nonclustered indexes, each b-tree leaf contains a reference to the database record that it represents. In clustered indexes, each leaf contains the database record itself.

This explains why database tables can only contain one clustered index: a table’s data can only have one physical representation on disk. It also explains why looking up data with a clustered index is faster than looking it up with a nonclustered index.

## How the two interact

In SQL Server, unless you specify otherwise, a clustered index is automatically created on the column(s) that you specify to be the table’s primary key. However just because this is the default behaviour, it doesn’t mean that making the primary key the clustered index is always the right thing to do.

An example of where this may not be desirable is a situation where you need an identity column to make each database record unique (primary key), but where the vast majority of querying will not be on this identity column, but something else. Imagine a table of application logs consisting of a timestamp and a message. It is theoretically possible that two log entries could be created with the same timestamp and message, so using these two columns as the primary key isn’t a good idea. However if the log table is consumed by a service which gets log items for a certain duration of time, it would make sense to create the clustered index on the timestamp column as it would speed up these queries.

It’s important to note that if a clustered index isn’t completely unique, SQL Server will add a unique identifier behind the scenes to rows that have the same clustered index value to enforce a distinct sort order. As this has a small impact on performance, it’s best to choose clustered indexes that are unique. The vast majority of the time the best clustered index for a table will also be its primary key, but this isn’t always necessarily the case.

## A word on ID columns

Most folks will create database tables with auto-incrementing ID columns without batting an eye. Indeed, ID columns are usually a good idea, but it’s worth taking a moment when creating a table to think about whether or not one is really necessary.

The best way to think of ID values are as pre-packaged primary keys. To determine if you need one, you need to think not only about what data is stored in the table, but also about how the table is going to be accessed. For example you may have a users table where each row could be identified by a first name, last name, address, and date of birth. While you could indeed eschew an ID column and create a primary key on these four fields, it may be better to create an ID column anyways if your application needs an easy way to refer to a given user. This is a common requirement when building APIs, for example.

This brings me to a very simple idea that unfortunately is often not taken seriously enough when designing SQL databases. **Databases should be designed to meet the needs of their consuming applications.** While building a database by using all of the defaults is often good enough for small projects, there’s usually a good amount of free performance that’s there for the taking if you take the time to think about how records are going to be queried and manipulated, and design it accordingly.
