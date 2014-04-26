---
layout: post
title: ! 'Migrating an Azure MySQL database'
category: Microsoft Azure
tags: [azure, mysql, cleardb, migration]
comments: true
share: true
---
## Disclaimer

*I make no guarantees about the reliability of this migration procedure. This is the process that I followed, and while it did work for me that does not in any way mean that you will obtain the same results. In short, use at your own risk.*

## The strategy

I recently had to migrate this blog from one Azure account to another. While the site migration wasn’t terribly fascinating, the database migration was a task unto itself. Because Microsoft provides MySQL databases to Azure users via a third party (ClearDB), they do not support migrating MySQL databases like they do web roles, SQL server databases, and the like. I therefore had to manage the migration myself. This is how I did it.

<ol>
<li>Create a new database in the target Azure account to migrate to</li>
<li>Get the credentials for the origin and target databases</li>
<li>Run a migration script</li>
</ol>
Step 1 is fairly simple and needs no explanation. Steps 2 and 3, however, are worth looking at in greater depth.

## Getting to the database

The first step to migrating the database is getting the required credentials. Specifically, the following information is required:

+ Database name
+ Host name
+ Username
+ Password

You can get these values by logging in to the Windows Azure management portal, finding the entry for the MySQL database (in my case it was a "Linked Resource" associated with my Azure Web Site) and clicking on the DB name. This will take you to the ClearDB website. Click on the **Endpoint Information** tab, and voil&agrave;.

![The ClearDB admin interface]({{ site.url }}/images/2014-02-14-migrating-an-azure-mysql-database/0.png)

## The script

In order to perform the actual migration, you'll need mysqldump.exe on your machine. If you have MySQL installed, you'll be able to find it at **C:\Program Files\MySQL\MySQL Server 5.5\bin**. Open a command prompt, navigate to the directory where mysqldump lives, and then execute the following command:

> mysqldump --single-transaction -u **[Origin Username]** –p**[Origin Password]** -h **[Origin Hostname]** **[Origin Database Name]** | mysql -h **[Target Hostname]** -u **[Target Username]** –p**[Target Password]** -D **[Target Database]**

Don’t worry, I too got a headache when I first laid eyes on it. There are three things that I must note about this before continuing on.

+ There is no space between the `–p` switch and the database password. That is intentional.
+ [This command will apparently delete data not covered by the backup in the target database](http://stackoverflow.com/q/5475200/1068266). I have assumed here that you are backing up to a new database containing no prior data.
+ I understand that it is often considered bad practice to embed the password directly in the command as I have done here. However, when I included just the password switch without supplying a password, the command line prompted me for the passwords to both databases simultaneously which understandably resulted in an error. I’m no DBA, so it’s perfectly possible that there is a way around this. If you know of one, leave a comment!

Anyways, launch the command with the arguments specified, and if your database isn't too large, in a matter of minutes all your data will, with a little luck, have successfully been migrated from one database to another.

## A word on Azure migration

If you ask Microsoft to migrate your Azure resources from one account to another, they will kindly inform you that the target account must be empty. They will also inform you that your MySQL database will be lost in the migration process (at least that’s what they told me). What this means for you, the account owner, is that you basically have two migration strategies to consider.

+ Migrate everything yourself, and delete the old stuff when you’re done.
+ Migrate your MySQL database to a backup DB, get Microsoft to perform the rest of the migration, and then perform the MySQL migration process once more to restore the backup to your new account.

In my case I chose option 1, but the decision is an important one and needs to be made on a case-by-case basis. If you're in a similar scenario, be sure to give this some thought, and in all cases be sure to back up liberally so that you're covered just in case things go haywire halfway through.

Good luck, and happy migration!