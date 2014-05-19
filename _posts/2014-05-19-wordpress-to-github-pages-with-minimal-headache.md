---
layout: post
title: WordPress to GitHub Pages with minimal headache
category: Miscellaneous
tags: [wordpress, github, jekyll]
comments: true
share: true
---

WordPress to GitHub Pages with minimal headache

About a month and a half ago I migrated this blog from an Azure-hosted WordPress setup to GitHub Pages, which runs on Jekyll. While I wouldn't describe the migration process as painful, I wish I would have had a guide such as this one when I began the transition as it would have certainly saved me a few headaches along the way. Hopefully this will be able to help someone in the same place that I was!

## Why migrate?

Before getting into the migration process, I will say that migrating is **totally worth it**. The process described herein may scare you a bit, but I promise you that once you're done you'll be very happy that you did it. Just to hammer the point home, here are a few things that I really love about GitHub pages now that I'm using them.

+ You're the master of your own content. Your blog becomes a GitHub repository that you can clone, fork and do what you want with. Everything is at your control. This isn't necessarily a very good system for non-programmers, but as a software developer I consider having this fine-grained control to be a great feature.
+ There is no black magic behind the scenes. Posts are written in Markdown with a bit of YAML header information, thrown into a folder, and that's it. This system is both easy to understand, and easy to work with.
+ GitHub handles the hosting! Hosting was easy on Azure, don't get me wrong, but GitHub completely removes hosting from the equation. The only thing that you have to worry about when on GitHub pages is *writing*.
+ It's free!

## Starting the migration

Alright, let's get down to business. If you've read up a bit on Jekyll you'll probably already know that there is a Jekyll "importer" for WordPress which will migrate your blog for you. Although it's not perfect, running the importer is an essential first step. The rest of the work will essentially consist of doing cleanup, fixing things here and there that the importer missed.

I won't go into installing and running the importer in this post. [You can find that information here](http://import.jekyllrb.com/docs/wordpress/). Follow the instructions provided, and before you know it you'll have in your hands a rough translation of your WordPress blog in Jekyll.

## The HTML problem

Now that you've got your blog in Jekyll format, it's time to start cleaning it up a bit. One of the biggest headaches when migrating is converting old HTML posts into Markdown. Many tools exist which automate this. The one that I used was [to-markdown](http://domchristie.github.io/to-markdown/). [Pandoc](http://johnmacfarlane.net/pandoc/), as suggested by [Phil Haack](http://haacked.com/) is apparently decent as well.

to-markdown did a pretty decent job of converting the majority of my content, but a few HTML tags did slip through the cracks. In addition, the old `[sourcecode]` blocks that I used for syntax highlighting while under WordPress  were also, understandably, still present after the transformation.

To try and automate the residual cleanup as much as possible I ended up writing a sed script.

To use the script, create a folder in your Jekyll blog root called `_migration`. Create a new text file called `sedscript.txt` and paste the following code into it.

{% highlight bash %}
# Erase
s:<p>|<a id="more"></a>|<a id="more-\d*"></a>::Ig

# HTML Replacements
s:</p>|<br\s?/>:\
:Ig
s:<br />::Ig
s:<h1>\(.*\)</h1>:\# \1\
:Ig
s:<h2>\(.*\)</h2>:\#\# \1\
:Ig
s:<h3>\(.*\)</h3>:\#\#\# \1\
:Ig
s:<h4>\(.*\)</h4>:\#\#\#\# \1\
:Ig
s:<h5>\(.*\)</h5>:\#\#\#\#\# \1\
:Ig
s:</?code>:`:Ig
s:</?i>|</?em>:*:Ig
s:</?b>|</?strong>:**:Ig
s:<a.*href="\(\S*\)".*>\(.*\)</a>:[\2](\1):Ig

# Character Replacements
s:&quot;:":Ig
s:&gt;:>:Ig
s:&lt;:<:Ig

# Syntax Highlighting
s:\[sourcecode language="\(\w*\)"\]:{% highlight \1 %}:Ig
s:\[sourcecode]:{% highlight text %}:Ig
s:\[/sourcecode\]:{% endhighlight %}:Ig
s:^\[\(\w*\)\]$:{% highlight \1 %}:Ig
s:^\[/\w*\]$:{% endhighlight %}:Ig
{% endhighlight %}

Now in the console navigate to your `_posts` folder and run the following command.

{% highlight bash %}
find ./_posts/ -type f -exec sed -i -f sedscript.txt {} \;
{% endhighlight %}

Note that if you're on Windows you can run this via the Git Bash window and all should be well.

Once run, about 95% of the rubbish that was left behind after the Markdown conversion should be cleaned up. You'll still want to run through your posts by hand just to make sure that everything looks good at this point, but by now you should be very close to having fully Markdownified posts.

## Metadata

Once your posts' content has been cleaned up, the next step is to attack their metadata. WordPress stores a lot of metadata about your blog posts. The Jekyll importer when run according to its instructions should retrieve this metadata and insert it into each post as YAML header information. Right after running the importer, your post headers will look something like the following.

{% highlight yaml %}
---
layout: post
status: publish
published: true
title: Aggregate Explained
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "For my money, LINQ is in itself a reason to program in C#. It saves time,
  reduces the amount of code needed to perform a given task, and is simple to debug.
  A function I really like is <strong>Aggregate</strong>. That said, I admit that
  it took a while for me to adopt it into my coding repertoire because its name doesn't
  do a very good job at describing what it does or how to use it.\r\n<h2>What does
  it do?</h2>\r\nAggregate takes a collection of small objects and combines them one
  by one into a single large object. It can take an optional “seed” value to which
  all the objects are added as well as a “selector” function which applies a final
  transformation on the combined object.\r\n<h2>How is it used?</h2>\r\nThe definition
  of Aggregate in its simplest form looks like this:\r\n"
wordpress_id: 72
wordpress_url: http://levibotelho.azurewebsites.net/?p=72
date: !binary |-
  MjAxMy0wNC0xNyAwODowNToyMSArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODowNToyMSArMDIwMA==
categories:
- C#
tags:
- c#
- linq
comments: []
---
{% endhighlight %}

You essentially want to transform them into this.

{% highlight yaml %}
---
layout: post
title: Aggregate Explained
category: Development
tags: [c#, linq]
comments: true
share: true
redirect_from: "/aggregate-explained/"
---
{% endhighlight %}

These are the changes that I made when transforming my posts' metadata:

+ I stripped out all unnecessary tags. This isn't strictly necessary, but it's nice to get rid of all that extra information that you just don't need.
+ I renamed the `categories` element `category`, and assigned only one category to each post. Alternatively, I apparently could have kept `categories` and passed the list of categories as an array. The important thing here is that the hyphenated list as returned by the importer is *not* valid.
+ I bundled the `tags` elements into an array. Same problem here with the hyphenated list.
+ I changed the `comments` value from an array of the comments left on the post to `true`. This is because the Jekyll theme that I use for my blog allows for Disqus comments which can be enabled or disabled on any given post by setting this value. (Disqus, in case you aren't aware, is a third-party service which essentially handles the comment section of your blog on your behalf.)
+ I added a `share` element and set its value to `true`. This is also specific to my theme, so like with `comments`, this may not apply to you.
+ I added the `redirect_from` element, which states which URLs should redirect to this post. This is essential as I need people who favourited urls on my old blog to be able to find the corresponding posts now that they have moved. This redirection functionality is not handled by Jekyll by default, but instead by a Jekyll plugin called Redirect From. [Very simple instructions on how to enable Redirect From on your GitHub pages site can be found here](https://help.github.com/articles/redirects-on-github-pages).

## Testing it out

Once you think that you've got everything worked out, you'll want to test out your blog locally before pushing it to GitHub. To get Jekyll running on Windows, you'll need to [follow the instructions outlined here](https://help.github.com/articles/using-jekyll-with-pages). Note that if you doing this on Windows you may need to execute `chcp 65001` in the command prompt before executing Jekyll to solve a character encoding issue. ([Thanks to José F. Romaniello for the fix.](http://joseoncode.com/2011/11/27/solving-utf-problem-with-jekyll-on-windows/)).

Fix up any remaining errors, and once you've got a local copy running you can push to GitHub! [Configure your custom domain if you have one using the instructions found here](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages), and you're good to go. Good luck!