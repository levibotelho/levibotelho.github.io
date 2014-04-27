---
layout: post
title: Create a WordPress blog on Azure in five minutes
category: Deployment
tags: [azure, wordpress]
comments: true
share: true
redirect_from: "/create-a-wordpress-blog-on-windows-azure-in-five-minutes/"
---
First off, this isn’t one of those fake “five minute” tutorials that take half an hour to go through. This really is easy. The only thing you’ll need for this tutorial is a Windows Azure account. If you don’t have one you can sign up for a one on [www.windowsazure.com](http://www.windowsazure.com) for free. Now then, let’s get started.

## Step 1: Log into your account

Head over to [manage.windowsazure.com](http://manage.windowsazure.com) and log in.

## Step 2: Go to the Web Sites menu and create a new site

The button to access the web sites menu is at the top left of the page…

![Azure web sites menu]({{ site.url }}/images/2013-11-07-create-a-wordpress-blog-on-windows-azure-in-five-minutes/0.png)

…and the button to create a new one is found at the bottom left.

![Create a new site]({{ site.url }}/images/2013-11-07-create-a-wordpress-blog-on-windows-azure-in-five-minutes/1.png)

## Step 3: Create a new website from the “Gallery”

![Create from gallery]({{ site.url }}/images/2013-11-07-create-a-wordpress-blog-on-windows-azure-in-five-minutes/2.png)

## Step 4: Select the WordPress App

Select “Blogs” from the category list at the left-hand side of the window and pick “WordPress” from the list.

![WordPress blog package]({{ site.url }}/images/2013-11-07-create-a-wordpress-blog-on-windows-azure-in-five-minutes/3.png)

## Step 5: Configure the site

Start by picking a URL. Your blog will officially be hosted at *yoururl.azurewebsites.net*, but you can choose to have this redirect to the domain name of your choice (more on that later). You’ll also need to specify that you want to create a new MySQL database for your blog. Finally, you’ll need to specify in what part of the world you’d like your blog to be hosted, and the Windows Azure account to associate with it.

![Configuring the site]({{ site.url }}/images/2013-11-07-create-a-wordpress-blog-on-windows-azure-in-five-minutes/4.png)

## Step 6: Accept the database TOS

![Accepting the database TOS]({{ site.url }}/images/2013-11-07-create-a-wordpress-blog-on-windows-azure-in-five-minutes/5.png)

## Step 7: Get writing!

Seriously, that's all there is to it! If you now look at the Web Sites page of the management portal you’ll see your new blog listed like so.

![The new site in the Azure management interface]({{ site.url }}/images/2013-11-07-create-a-wordpress-blog-on-windows-azure-in-five-minutes/6.png)

Clicking the provided address will take you to your blog where you’ll be met with a welcome page from WordPress which will allow you to create a blog admin account. You can then log in and write your first post!

## Custom domain names

If you’re serious about starting a blog, the one thing that you’ll be missing right now is a custom domain name. Sure, *myveryspecialblog.azurewebsites.net* is great to get started, but it’s not terribly aesthetic. I won’t go on explaining the whole process of configuring a custom domain [because Microsoft has already done so here](http://www.windowsazure.com/en-us/develop/net/common-tasks/custom-dns-web-site/), but the one thing I will mention is that in order to map your azurewebsites domain to your personal domain name you’ll need to change the “compute mode” of your blog from “Free” to “Shared”. Note that this change does come at a small, but very manageable cost. As an example, at the time of writing this blog which is hosted as a shared Azure Web Site costs a little less than €1.00 per hundred visitors to run.

