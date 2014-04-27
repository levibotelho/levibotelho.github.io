---
layout: post
title: Calculating Distances in CSS
category: Development
tags: [css]
comments: true
share: true
redirect_from: "/calculating-distances-in-css/"
---
I was originally planning this week to write a short introduction to Entity Framework, when I saw that I had received a seventh upvote on my answer to [this Stack Overflow question](http://stackoverflow.com/q/2434602/1068266).

What the OP wanted to do was simple: calculate a distance in CSS as a percentage value minus a set number of pixels. While this would seem to be a simple enough task, all the other answers provided a wide variety of tricks and hacks to get the job done. Before CSS 3 came along, these methods were indeed necessary. Now that we have CSS 3 however, there is a much, much simpler solution:

{% highlight css %}

height: calc(XX% - XXpx);

{% endhighlight %}

The calc function in CSS 3 allows you to multiply, divide, add and subtract virtually any combination of valid CSS units. You can even nest calcs inside one another should the need arise.
<a id="more"></a><a id="more-132"></a>
The only real drawback to calc is that at the current time it isn’t well supported enough to allow its adoption as a de-facto solution. Until that day comes, you should take advantage of the way in which CSS applies equivalent-priority style rules (last rule is applied) and use the following general syntax:

{% highlight css %}

width: 90%; /* An alternative for non-compliant browsers */
width: -moz-calc(95% - 5em); /* Firefox 4+ */
width: -webkit-calc(95% - 5em); /* Chrome 19+ */
width: calc(95% - 5em); /*IE9+, Firefox 16+ */

{% endhighlight %}

I recommend that you look at the aforementioned Stack Overflow question for non-compliant browser alternatives.

The one other thing worth mentioning is that rules containing calculated distances will obviously take more time to apply than their static counterparts. In my own experience I have never had any performance problems when using calc, but code like this

{% highlight css %}

width: calc(100% - 73%);

{% endhighlight %}

should evidently be avoided.

I have to say that I normally wouldn’t have considered this subject to be worthy of a blog post, but given the relatively high amount of attention that my very late-to-the-game S.O. answer has received, it appears that this CSS 3 feature isn’t as well-known as it should be. It is indispensable when creating full-screen page layouts, and I consider it a much needed addition to CSS.

