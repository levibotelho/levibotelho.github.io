---
layout: post
title: ! '[knockout.js] Foreach binding to an array of strings (or numbers, or bools…)'
category: Development
tags: [javascript, knockoutjs]
description: It's easy to slip up when you're learning Knockout and want to bind to an array. Here we walk through the binding process with an easy to understand example. 
comments: true
share: true
redirect_from: "/knockout-js-foreach-binding-to-an-array-of-strings-or-numbers-or-bools/"
---
I’m currently working on a web application where I have to write an interface that allows users to delete photos that they have uploaded to the site. The user interface and corresponding view model are based on the following design.

{% highlight html %}
<button data-bind="click: delete">Delete Photo</button>
<div data-bind="foreach: photos">
	<div>
		<span data-bind="text: $data"></span>
		<img data-bind="attr: {src: $data}, click: $root.select" />
	</div>
</div>
{% endhighlight %}

{% highlight javascript %}
function ViewModel() {
	var self = this;
	self.selected = ko.observable("");
	self.photos = ko.observableArray(["url0", "url1", "url2"]);
	self.select = function() {
		self.selected(this);
	}
	self.delete = function() {
		self.photos.remove(self.selected());
		self.selected(undefined);
	}
};
{% endhighlight %}

So, we have a collection of photo urls stored in an observable array of strings. By way of a foreach binding we create an image for each url which can be selected by clicking on it. Clicking on the delete button deletes the currently selected image, which works by removing it from the observable array and unselecting it.

Except that the code as it is written above doesn’t work. When we attempt to delete an image, the url is not correctly removed from the observable collection.

The reason for this is quite interesting. `remove()` works by searching the observable array for any members that are exactly equal to the argument passed to the method and removing them. Notice how I said *exactly* equals? Herein lies our problem. If we select the photo with the src attribute set to `url0` and then examine the value of `self.selected` using a JavaScript console, we get something that looks like the following

{% highlight javascript %}
String {0: "u", 1: "r", 2: "l", 3: "1", length: 4}
{% endhighlight %}

What we have here is an instance of the `String` class. This is *not* the same thing as the `string` primitive type which is how we normally picture a string in JavaScript. The problem is that our observable array of urls contains string primitives. If we compare a string primitive to an equivalent `String` object using the “strict equal” operator (`===`), the comparison returns false. This is the comparison that `remove` uses, and it is for this reason that our url isn’t removed from the observable array when we delete the image. As far as our JavaScript is concerned, the value we wish to remove from the array simply doesn't exist!

So how do we get around this? Well, if we set `self.selected` in the `data-bind` attribute using an inline function instead of in the view model, the string primitive is stored in the variable and not the object. This isn’t a very good solution however, because it requires us to scatter our view model logic throughout the HTML page. A better way to accomplish this is to call `valueOf()` on `this` when we are setting the `selected` variable.

{% highlight javascript %}
self.selected(this.valueOf());
{% endhighlight %}

`valueOf()` obtains the primitive value from the `String` class, and setting this as our selected photo url will ensure that we can remove it from the observable array when we need to. `valueOf()` is also a good choice because it works for all class/primitive type pairs, not just with `String` and `string`. This is important because we get the same behaviour when working with arrays of numbers or bools as well.