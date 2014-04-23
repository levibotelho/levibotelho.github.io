---
layout: post
status: publish
published: true
title: How to use lookarounds in regular expressions
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "The trick to understanding lookarounds is understanding that they are
  made up of two components: the condition and the capture. Let’s explore this using
  an example. Let’s say that we want to find all words in a phrase that start with
  the lowercase letter w. First, let’s write an expression to capture all lowercase
  w’s at the beginning of words.\r\n\r\n[perl]\r\n\\bw\r\n[/perl]\r\n\r\nNow, let’s
  turn this into a condition by wrapping it in a positive lookahead.\r\n\r\n[perl]\r\n(?=\\bw)\r\n[/perl]\r\n\r\nTranslated
  into plain English, this means “When we find a w at the beginning of a word, execute
  what comes next.” To finish our expression, we’ll now write the “what comes next”
  bit. That is, a statement to capture the entire word in question.\r\n"
wordpress_id: 1942
wordpress_url: http://www.levibotelho.com/?p=1942
date: !binary |-
  MjAxMy0wNy0zMSAxMDoxMTozNCArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNy0zMSAwODoxMTozNCArMDIwMA==
categories:
- Miscellaneous Development
tags:
- regex
comments: []
---
<p>The trick to understanding lookarounds is understanding that they are made up of two components: the condition and the capture. Let’s explore this using an example. Let’s say that we want to find all words in a phrase that start with the lowercase letter w. First, let’s write an expression to capture all lowercase w’s at the beginning of words.</p>
<p>[perl]<br />
\bw<br />
[/perl]</p>
<p>Now, let’s turn this into a condition by wrapping it in a positive lookahead.</p>
<p>[perl]<br />
(?=\bw)<br />
[/perl]</p>
<p>Translated into plain English, this means “When we find a w at the beginning of a word, execute what comes next.” To finish our expression, we’ll now write the “what comes next” bit. That is, a statement to capture the entire word in question.<br />
<a id="more"></a><a id="more-1942"></a><br />
[perl]<br />
\w*<br />
[/perl]</p>
<p>Now let’s put them together.</p>
<p>[perl]<br />
(?=\bw)\w*<br />
[/perl]</p>
<p>And we’re done.</p>
<h2>A less contrived example </h2>
<p>Now that we understand the basics of lookarounds, let’s use them to solve the common problem of password validation. For this example, we have two simple constraints:</p>
<ul>
<li>The password must contain at least one number.</li>
<li>The password must contain at least one letter.</li>
<li>The password must not contain any whitespace.</li>
</ul>
<p>To be able to do this, we need to use lookarounds. Let’s start by capturing anything but whitespace.<br />
[perl]<br />
\S*<br />
[/perl]<br />
Now let’s add our constraints. Let’s start by requiring that it contain at least one number. We’ll use a lookahead in a bit of a creative way for this. If you think about it for a minute, you’ll realize that “The password must contain at least one number” is equivalent to “The password must start with a string of characters that ends in a number”. Once we make this connection, writing the condition is simple:<br />
[perl]<br />
(?=.*\d)\S*<br />
[/perl]<br />
We now have a statement that says “When we find a string of characters that ends in a digit, capture the whole thing.” Let’s now apply the exact same logic to require that the password also contain at least one letter.<br />
[perl]<br />
(?=.*[a-zA-Z])(?=.*\d)\S*<br />
[/perl]<br />
Notice that by chaining these lookarounds together, we have essentially added an “and” to our condition. It is important to understand that chaining several lookarounds together simply adds complexity to the precondition for a match. We aren’t introducing a notion of order here by having one lookaround come before the other. We could just as well write the above regex as follows and still obtain the same result:<br />
[perl]<br />
(?=.*\d)(?=.*[a-zA-Z])\S*<br />
[/perl]<br />
To finish off our expression, we add anchors to tie this regex to the start and end of our string:<br />
[perl]<br />
^(?=.*\d)(?=.*[a-zA-Z])\S*$<br />
[/perl]<br />
That's all there is to it!</p>
<h2>Final word</h2>
<p>I hope that this article has been helpful in understanding the basics of lookarounds in regular expressions. I intended it to be a simple introduction to the concept made accessible to the average developer who might not use regular expressions very often. A much more thorough look at lookarounds (and many other regular expression topics) can be found <a href="http://www.rexegg.com/regex-lookarounds.html" title="Regex Lookarounds" target="_blank">here</a>. I strongly encourage you to take a look.</p>
