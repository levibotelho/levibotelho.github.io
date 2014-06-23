---
layout: post
title: Recover lost Git commits 
category: Development
tags: [git]
comments: true
share: true
---

Have you ever done a `git reset --hard` without thinking it all the way through and found yourself having lost work that you might have been better off keeping? Well, Git actually provides an easy way to recover lost commits. Say that we have a repository with a history that looks like this.

{% highlight bash %}
a63d97b Added more data to newfile
b28b586 Added data to newfile
5760b1c Added newfile
{% endhighlight %}

Now let's say that we do a `git reset --hard 5760b1c`. Our history now looks like this.

{% highlight bash %}
5760b1c Added newfile
{% endhighlight %}

To recover those two commits that we just wiped, we simply need to run the `git reflog` command, which gives us the following output.

{% highlight bash %}
5760b1c HEAD@{0}: reset: moving to 5760b1c
a63d97b HEAD@{1}: commit: Added more data to newfile
b28b586 HEAD@{2}: commit: Added data to newfile
5760b1c HEAD@{3}: commit (initial): Added newfile
{% endhighlight %}

Here we have a history of the actions that we've taken in our repository. To get those two lost commits back, we have two choices. First, we can create a new branch that starts from the latest commit that we want to recover like so.

{% highlight bash %}
git checkout -b recovery a63d97b
{% endhighlight %}

If we look at the log for the `recovery` branch, we see that the history is exactly the same as our original master history.

{% highlight bash %}
a63d97b Added more data to newfile
b28b586 Added data to newfile
5760b1c Added newfile
{% endhighlight %}

Our other option is to simply perform a hard reset back to our desired commit (`git reset --hard a63d97b` in this case). Needless to say, if you have any work in progress you'll lose it by doing the hard reset, so you'll want to go with option 1 if that's the case.

One final note: the Git reflog is not kept forever. By default, entries that are more than 30 days old become eligible for deletion from the reflog (and will be deleted if you run `git gc`, for example), however this date can be changed at will. So if you intend on using this feature, don't use `git gc` until you are satisfied with the state of your repository. 