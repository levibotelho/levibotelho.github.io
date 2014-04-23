---
layout: post
status: publish
published: true
title: ! '[knockout.js] Foreach binding to an array of strings (or numbers, or bools…)'
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "I’m currently working on a web application where I have to write an interface
  that allows users to delete photos that they have uploaded to the site. The user
  interface and corresponding view model are based on the following design.\n\n[html]\r\n&lt;button
  data-bind=&quot;click: delete&quot;&gt;Delete Photo&lt;/button&gt;\r\n&lt;div data-bind=&quot;foreach:
  photos&quot;&gt;\r\n\t&lt;div&gt;\r\n\t\t&lt;span data-bind=&quot;text: $data&quot;&gt;&lt;/span&gt;\r\n\t\t&lt;img
  data-bind=&quot;attr: {src: $data}, click: $root.select&quot; /&gt;\r\n\t&lt;/div&gt;\r\n&lt;/div&gt;\r\n[/html]\n\n[javascript]\r\nfunction
  ViewModel() {\r\n\tvar self = this;\r\n\tself.selected = ko.observable(&quot;&quot;);\r\n\tself.photos
  = ko.observableArray([&quot;url0&quot;, &quot;url1&quot;, &quot;url2&quot;]);\r\n\tself.select
  = function() {\r\n\t\tself.selected(this);\r\n\t}\r\n\tself.delete = function()
  {\r\n\t\tself.photos.remove(self.selected());\r\n\t\tself.selected(undefined);\t\t\t\t\r\n\t}\r\n};\r\n[/javascript]\n\nSo,
  we have a collection of photo urls stored in an observable array of strings. By
  way of a foreach binding we create an image for each url which can be selected by
  clicking on it. Clicking on the delete button deletes the currently selected image,
  which works by removing it from the observable array and unselecting it.\n\nExcept
  that the code as it is written above doesn’t work. When we attempt to delete an
  image, the url is not correctly removed from the observable collection.\n"
wordpress_id: 4322
wordpress_url: http://www.levibotelho.com/?p=4322
date: !binary |-
  MjAxNC0wMS0yOSAyMjo0ODo0MiArMDEwMA==
date_gmt: !binary |-
  MjAxNC0wMS0yOSAyMTo0ODo0MiArMDEwMA==
categories:
- Front-end Web
tags:
- javascript
- knockout.js
comments: []
---
<p>I’m currently working on a web application where I have to write an interface that allows users to delete photos that they have uploaded to the site. The user interface and corresponding view model are based on the following design.</p>
<p>[html]<br />
&lt;button data-bind=&quot;click: delete&quot;&gt;Delete Photo&lt;/button&gt;<br />
&lt;div data-bind=&quot;foreach: photos&quot;&gt;<br />
	&lt;div&gt;<br />
		&lt;span data-bind=&quot;text: $data&quot;&gt;&lt;/span&gt;<br />
		&lt;img data-bind=&quot;attr: {src: $data}, click: $root.select&quot; /&gt;<br />
	&lt;/div&gt;<br />
&lt;/div&gt;<br />
[/html]</p>
<p>[javascript]<br />
function ViewModel() {<br />
	var self = this;<br />
	self.selected = ko.observable(&quot;&quot;);<br />
	self.photos = ko.observableArray([&quot;url0&quot;, &quot;url1&quot;, &quot;url2&quot;]);<br />
	self.select = function() {<br />
		self.selected(this);<br />
	}<br />
	self.delete = function() {<br />
		self.photos.remove(self.selected());<br />
		self.selected(undefined);<br />
	}<br />
};<br />
[/javascript]</p>
<p>So, we have a collection of photo urls stored in an observable array of strings. By way of a foreach binding we create an image for each url which can be selected by clicking on it. Clicking on the delete button deletes the currently selected image, which works by removing it from the observable array and unselecting it.</p>
<p>Except that the code as it is written above doesn’t work. When we attempt to delete an image, the url is not correctly removed from the observable collection.<br />
<a id="more"></a><a id="more-4322"></a><br />
The reason for this is quite interesting. <code>remove()</code> works by searching the observable array for any members that are exactly equal to the argument passed to the method and removing them. Notice how I said <em>exactly</em> equals? Herein lies our problem. If we select the photo with the src attribute set to <code>url0</code> and then examine the value of <code>self.selected</code> using a JavaScript console, we get something that looks like the following</p>
<p>[javascript]<br />
String {0: &quot;u&quot;, 1: &quot;r&quot;, 2: &quot;l&quot;, 3: &quot;1&quot;, length: 4}<br />
[/javascript]</p>
<p>What we have here is an instance of the <code>String</code> class. This is <em>not</em> the same thing as the <code>string</code> primitive type which is how we normally picture a string in JavaScript. The problem is that our observable array of urls contains string primitives. If we compare a string primitive to an equivalent <code>String</code> object using the “strict equal” operator ( <code>===</code> ), the comparison returns false. This is the comparison that <code>remove</code> uses, and it is for this reason that our url isn’t removed from the observable array when we delete the image. As far as our JavaScript is concerned, the value we wish to remove from the array simply doesn't exist!</p>
<p>So how do we get around this? Well, if we set <code>self.selected</code> in the <code>data-bind</code> attribute using an inline function instead of in the view model, the string primitive is stored in the variable and not the object. This isn’t a very good solution however, because it requires us to scatter our view model logic throughout the HTML page. A better way to accomplish this is to call <code>valueOf()</code> on <code>this</code> when we are setting the <code>selected</code> variable.</p>
<p>[javascript]<br />
self.selected(this.valueOf());<br />
[/javascript]</p>
<p><code>valueOf()</code> obtains the primitive value from the <code>String</code> class, and setting this as our selected photo url will ensure that we can remove it from the observable array when we need to. <code>valueOf()</code> is also a good choice because it works for all class/primitive type pairs, not just with <code>String</code> and <code>string</code>. This is important because we get the same behaviour when working with arrays of numbers or bools as well.</p>
