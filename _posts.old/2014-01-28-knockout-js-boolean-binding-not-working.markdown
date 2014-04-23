---
layout: post
status: publish
published: true
title: ! '[knockout.js] Boolean binding not working'
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
wordpress_id: 4262
wordpress_url: http://www.levibotelho.com/?p=4262
date: !binary |-
  MjAxNC0wMS0yOCAxMzoxNjowOSArMDEwMA==
date_gmt: !binary |-
  MjAxNC0wMS0yOCAxMjoxNjowOSArMDEwMA==
categories:
- Front-end Web
tags:
- javascript
- knockout.js
comments: []
---
<p>Here's a quick fix if you’re doing data binding in knockout and wondering why a binding like the following isn’t working:</p>
<p>[html]<br />
&lt;div data-bind=&quot;visible: !isMessageHidden&quot;&gt;</p>
<p>&lt;script&gt;<br />
// In your view model<br />
self.isMessageHidden: ko.observable(true) };<br />
// ...<br />
&lt;/script&gt;<br />
[/html]</p>
<p>You need to add a set of parentheses after the <code>isMessageHidden</code> like so.</p>
<p>[html]<br />
&lt;div data-bind=&quot;visible: !isMessageHidden()&quot;&gt;<br />
[/html]</p>
<p>The key to understanding this is understanding that <code>isMessageHidden</code> is not a boolean value, but an observable which stores a boolean value. If we simply refer to this observable by name in our data-binding code, then knockout will infer that we want to retrieve the stored value. However, if we wish to refer to the stored value within a statement, such as <code>!isMessageHidden()</code>, then the parentheses are required in order to indicate that we want the stored value and not the observable itself. This tripped me up a few times when I was learning knockout. Hope this helps somebody!</p>
