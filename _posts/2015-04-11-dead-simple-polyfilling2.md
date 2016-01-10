---
layout: post
title: Dead simple polyfilling 2
category: Development
tags: [javascript]
description: Polyfill missing browser features with an 8-line JavaScript function.
comments: true
share: true
---

As more and more EcmaScript 6 features are being implemented in modern web browsers, developers can begin to strip unneeded libraries from their applications. However in many cases, implementations of these features are not present in browsers that still need supporting.

An example of one of these features is the Promise API. Promises have been sorely lacking from JavaScript for quite some time, and will finally make their debut in ES6. Modern versions of Chrome and Firefox already support promises, but Internet Exporer 11, as recent as it is, does not yet have an implementation.

In order to use ES6 promises in browsers that don't support them, a polyfill (shim that provides the missing functionality) is necessary.

The problem with polyfills is that by their very definition, they aren't always necessary. It's absolutely pointless to include them when the browser provides a native implementation of their functionality, and in such cases they simply bloat the web app for nothing. To solve this problem, many libraries have popped up which allow conditional script loading. Frankly though, these libraries are often completely unnecessary. The following function is 8 lines long and will conditionally load any script file that your web app might need.

{% highlight javascript %}
function loadScript(condition, source) {
    if (condition) {
        var script = document.createElement("script");
        script.type = "text/javascript";
        script.src = source;
        document.body.appendChild(script);
    }
}
{% endhighlight %}

To use it, simply find a polyfill for the feature you're missing, and in your web page call the function as follows.


{% highlight javascript %}
loadScript(typeof(Promise) === "undefined", "/Content/lib/promise.min.js");
{% endhighlight %}

Here we're checking if `Promise` is defined. If it's not, then the page will load a [Promise polyfill](https://github.com/lahmatiy/es6-promise-polyfill). If it is, then we move on and no loading takes place.

Easy!