---
layout: post
status: publish
published: true
title: Calculating Distances in CSS
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "I was originally planning this week to write a short introduction to Entity
  Framework, when I saw that I had received a seventh upvote on my answer to <a href=\"http://stackoverflow.com/q/2434602/1068266\"
  target=\"_blank\">this Stack Overflow question</a>.\r\n\r\nWhat the OP wanted to
  do was simple: calculate a distance in CSS as a percentage value minus a set number
  of pixels. While this would seem to be a simple enough task, all the other answers
  provided a wide variety of tricks and hacks to get the job done. Before CSS 3 came
  along, these methods were indeed necessary. Now that we have CSS 3 however, there
  is a much, much simpler solution:\r\n\r\n[sourcecode language=\"css\"]\r\n\r\nheight:
  calc(XX% - XXpx);\r\n\r\n[/sourcecode]\r\n\r\nThe calc function in CSS 3 allows
  you to multiply, divide, add and subtract virtually any combination of valid CSS
  units. You can even nest calcs inside one another should the need arise.\r\n"
wordpress_id: 132
wordpress_url: http://levibotelho.azurewebsites.net/?p=132
date: !binary |-
  MjAxMy0wNC0xNyAxMDozNDoyOSArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODozNDoyOSArMDIwMA==
categories:
- Front-end Web
tags:
- css
comments: []
---
<p>I was originally planning this week to write a short introduction to Entity Framework, when I saw that I had received a seventh upvote on my answer to <a href="http://stackoverflow.com/q/2434602/1068266" target="_blank">this Stack Overflow question</a>.</p>
<p>What the OP wanted to do was simple: calculate a distance in CSS as a percentage value minus a set number of pixels. While this would seem to be a simple enough task, all the other answers provided a wide variety of tricks and hacks to get the job done. Before CSS 3 came along, these methods were indeed necessary. Now that we have CSS 3 however, there is a much, much simpler solution:</p>
<p>[sourcecode language="css"]</p>
<p>height: calc(XX% - XXpx);</p>
<p>[/sourcecode]</p>
<p>The calc function in CSS 3 allows you to multiply, divide, add and subtract virtually any combination of valid CSS units. You can even nest calcs inside one another should the need arise.<br />
<a id="more"></a><a id="more-132"></a><br />
The only real drawback to calc is that at the current time it isn’t well supported enough to allow its adoption as a de-facto solution. Until that day comes, you should take advantage of the way in which CSS applies equivalent-priority style rules (last rule is applied) and use the following general syntax:</p>
<p>[sourcecode language="css"]</p>
<p>width: 90%; /* An alternative for non-compliant browsers */<br />
width: -moz-calc(95% - 5em); /* Firefox 4+ */<br />
width: -webkit-calc(95% - 5em); /* Chrome 19+ */<br />
width: calc(95% - 5em); /*IE9+, Firefox 16+ */</p>
<p>[/sourcecode]</p>
<p>I recommend that you look at the aforementioned Stack Overflow question for non-compliant browser alternatives.</p>
<p>The one other thing worth mentioning is that rules containing calculated distances will obviously take more time to apply than their static counterparts. In my own experience I have never had any performance problems when using calc, but code like this</p>
<p>[sourcecode language="css"]</p>
<p>width: calc(100% - 73%);</p>
<p>[/sourcecode]</p>
<p>should evidently be avoided.</p>
<p>I have to say that I normally wouldn’t have considered this subject to be worthy of a blog post, but given the relatively high amount of attention that my very late-to-the-game S.O. answer has received, it appears that this CSS 3 feature isn’t as well-known as it should be. It is indispensable when creating full-screen page layouts, and I consider it a much needed addition to CSS.</p>
