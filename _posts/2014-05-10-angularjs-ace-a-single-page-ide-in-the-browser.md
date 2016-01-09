---
layout: post
title: ! '[AngularJS/Ace] A single page IDE in the browser'
category: Development
tags: [angularjs, ace, learnangular]
description: JavaScript sure has come a long way in the past few years! Create an IDE right in your browser using AngularJS and the outstanding Ace project.
comments: true
share: true
---

Every once in a while you come across a technology that makes you stop and think about just how far web development has come in the past few years. For me, the Ace project was one of those technologies. If you're not familiar with it, Ace is a "high performance code editor for the web". It allows you to create sites that let the user write code directly in their browser. [LearnAngular](http://www.learn-angular.org) makes extensive use of Ace.

In this article we're going to look at how to integrate an Ace editor into an AngularJS app with the help of AngularUI.

## Step 1 - Get the components

The first thing we need to do is get a hold of the three libraries that we are going to need:

+ AngularJS - [angularjs.org](https://angularjs.org/), [Google Hosted Libraries](https://developers.google.com/speed/libraries/devguide#angularjs), [cdnjs](http://cdnjs.com/libraries/angular.js), `bower install angular`
+ Ace - [ace.c9.io](http://ace.c9.io/), [cdnjs](http://cdnjs.com/libraries/ace), `bower install ace`
+ ui-ace - [GitHub](https://github.com/angular-ui/ui-ace), `bower install angular-ui-ace\#bower`

Reference them in your application in the following order.

1. AngularJS
2. Ace
3. ui-ace

## Step 2 - Write the HTML

{% highlight html %}
<div class="editor" ui-ace="{ mode: 'javascript', theme: 'monokai' }" ng-model="code"></div>
{% endhighlight %}

Believe it or not, this is all the HTML you'll need in order to integrate Ace into your site. Let's quickly run through what we've done.

The `editor` class is simply applied so that we can position and size the editor using a bit of CSS that we'll see in a moment.

The `ui-ace` directive transforms our `div` into an Ace editor. Here we're passing it parameters to set a programming language and theme. Ace [supports a ton of languages](https://github.com/ajaxorg/ace/tree/master/lib/ace/mode) and has [quite a few themes as well](https://github.com/ajaxorg/ace/tree/master/lib/ace/theme). It's worth taking a moment to check them out, if only to get a feel for how fully-featured the Ace project really is.

Finally, we add the ubiquitous `ng-model` directive so that whatever the user types into the code editor is bound to our Angular `$scope`. We can also evidently use this to make the editor display some text by default.

## Step 3 - Inject the "ui-ace" directive into your controller module

{% highlight javascript %}
angular.module("app", ['ui.ace'])
	.controller("controller", ["$scope", function($scope) {
		$scope.code = "alert('hello world');";
	}]);
{% endhighlight %}

## Step 4 - Add a touch of CSS

The CSS that we're going to apply to the editor serves to give it size and position on our page. As mentioned a moment ago, the actual look of the editor is controlled by applying a theme.

{% highlight css %}
.editor { 
	height: 200px;
}
{% endhighlight %}

## Step 5 - Try it out

That's all there is to do! In just a few lines of code we've integrated an extremely rich code editor into a web page, and can go on and do whatever we'd like with the code that our users write. Pretty cool if you ask me!