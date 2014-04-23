---
layout: post
status: publish
published: true
title: ! '[Azure/ClearDB] Migrating a MySQL database'
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! '<h2>Disclaimer</h2>


  <em>I make no guarantees about the reliability of this migration procedure. This
  is the process that I followed, and while it did work for me that does not in any
  way mean that you will obtain the same results. In short, use at your own risk.</em>


  <h2>The strategy</h2>


  I recently had to migrate this blog from one Azure account to another. While the
  site migration wasn’t terribly fascinating, the database migration was a task unto
  itself. Because Microsoft provides MySQL databases to Azure users via a third party
  (ClearDB), they do not support migrating MySQL databases like they do web roles,
  SQL server databases, and the like. I therefore had to manage the migration myself.
  This is how I did it.


  <ol>

  <li>Create a new database in the target Azure account to migrate to</li>

  <li>Get the credentials for the origin and target databases</li>

  <li>Run a migration script</li>

  </ol>


  Step 1 is fairly simple and needs no explanation. Steps 2 and 3, however, are worth
  looking at in greater depth.

'
wordpress_id: 4461
wordpress_url: http://www.levibotelho.com/?p=4461
date: !binary |-
  MjAxNC0wMi0xNCAxNDoxNDoxNCArMDEwMA==
date_gmt: !binary |-
  MjAxNC0wMi0xNCAxMzoxNDoxNCArMDEwMA==
categories:
- Database
- Windows Azure
tags:
- azure
- mysql
- cleardb
- migration
comments:
- id: 32171
  author: Marl Kons
  author_email: DuckettWilk73@hotmail.com
  author_url: http://sdrytdys
  date: !binary |-
    MjAxNC0wMy0wNyAwMzoyNjoyNiArMDEwMA==
  date_gmt: !binary |-
    MjAxNC0wMy0wNyAwMjoyNjoyNiArMDEwMA==
  content: Hey, I just noticed that occasionally this website shows a 403 server error
    message. I figured you would be keen to know. Regards
- id: 32201
  author: Levi
  author_email: levi_botelho@outlook.com
  author_url: ''
  date: !binary |-
    MjAxNC0wMy0wNyAwOToxNTozNCArMDEwMA==
  date_gmt: !binary |-
    MjAxNC0wMy0wNyAwODoxNTozNCArMDEwMA==
  content: Thank you very much for the heads up. I've been getting a bit more traffic
    than usual lately so I'll take a look at the logs and try to figure out what's
    going on. Very much appreciated.
---
<h2>Disclaimer</h2>
<p><em>I make no guarantees about the reliability of this migration procedure. This is the process that I followed, and while it did work for me that does not in any way mean that you will obtain the same results. In short, use at your own risk.</em></p>
<h2>The strategy</h2>
<p>I recently had to migrate this blog from one Azure account to another. While the site migration wasn’t terribly fascinating, the database migration was a task unto itself. Because Microsoft provides MySQL databases to Azure users via a third party (ClearDB), they do not support migrating MySQL databases like they do web roles, SQL server databases, and the like. I therefore had to manage the migration myself. This is how I did it.</p>
<ol>
<li>Create a new database in the target Azure account to migrate to</li>
<li>Get the credentials for the origin and target databases</li>
<li>Run a migration script</li>
</ol>
<p>Step 1 is fairly simple and needs no explanation. Steps 2 and 3, however, are worth looking at in greater depth.<br />
<a id="more"></a><a id="more-4461"></a></p>
<h2>Getting to the database</h2>
<p>The first step to migrating the database is getting the required credentials. Specifically, the following information is required:</p>
<ul>
<li>Database name</li>
<li>Host name</li>
<li>Username</li>
<li>Password</li>
</ul>
<p>You can get these values by logging in to the Windows Azure management portal, finding the entry for the MySQL database (in my case it was a "Linked Resource" associated with my Azure Web Site) and clicking on the DB name. This will take you to the ClearDB website. Click on the <strong>Endpoint Information</strong> tab, and voil&agrave;.</p>
<p><a href="http://www.levibotelho.com/wp-content/uploads/2014/02/Migrating-ClearDB.png"><img src="http://www.levibotelho.com/wp-content/uploads/2014/02/Migrating-ClearDB.png" alt="Migrating ClearDB" /></a></p>
<h2>The script</h2>
<p>In order to perform the actual migration, you'll need mysqldump.exe on your machine. If you have MySQL installed, you'll be able to find it at <strong>C:\Program Files\MySQL\MySQL Server 5.5\bin</strong>. Open a command prompt, navigate to the directory where mysqldump lives, and then execute the following command:</p>
<blockquote><p>
  mysqldump --single-transaction -u <strong>[Origin Username]</strong> –p<strong>[Origin Password]</strong> -h <strong>[Origin Hostname]</strong> <strong>[Origin Database Name]</strong> | mysql -h <strong>[Target Hostname]</strong> -u <strong>[Target Username]</strong> –p<strong>[Target Password]</strong> -D <strong>[Target Database]</strong>
</p></blockquote>
<p>Don’t worry, I too got a headache when I first laid eyes on it. There are three things that I must note about this before continuing on.</p>
<ul>
<li>There is no space between the <code>–p</code> switch and the database password. That is intentional.</li>
<li><a href="http://stackoverflow.com/q/5475200/1068266" title="MySQL restoring a database via mysqldump - Does it overwrite the different destination tables?">This command will apparently delete data not covered by the backup in the target database</a>. I have assumed here that you are backing up to a new database containing no prior data.</li>
<li>I understand that it is often considered bad practice to embed the password directly in the command as I have done here. However, when I included just the password switch without supplying a password, the command line prompted me for the passwords to both databases simultaneously which understandably resulted in an error. I’m no DBA, so it’s perfectly possible that there is a way around this. If you know of one, leave a comment!</li>
</ul>
<p>Anyways, launch the command with the arguments specified, and if your database isn't too large, in a matter of minutes all your data will, with a little luck, have successfully been migrated from one database to another.</p>
<h2>A word on Azure migration</h2>
<p>If you ask Microsoft to migrate your Azure resources from one account to another, they will kindly inform you that the target account must be empty. They will also inform you that your MySQL database will be lost in the migration process (at least that’s what they told me). What this means for you, the account owner, is that you basically have two migration strategies to consider.</p>
<ol>
<li>Migrate everything yourself, and delete the old stuff when you’re done.</li>
<li>Migrate your MySQL database to a backup DB, get Microsoft to perform the rest of the migration, and then perform the MySQL migration process once more to restore the backup to your new account.</li>
</ol>
<p>In my case I chose option 1, but the decision is an important one and needs to be made on a case-by-case basis. If you're in a similar scenario, be sure to give this some thought, and in all cases be sure to back up liberally so that you're covered just in case things go haywire halfway through.</p>
<p>Good luck, and happy migration!</p>
