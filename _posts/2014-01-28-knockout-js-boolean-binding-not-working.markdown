---
layout: post
title: ! '[knockout.js] Boolean binding not working'
category: Development
tags: [javascript, knockout.js]
comments: true
share: true
---
Here's a quick fix if you’re doing data binding in knockout and wondering why a binding like the following isn’t working:

{% highlight html %}
<div data-bind="visible: !isMessageHidden">

<script>
// In your view model
self.isMessageHidden: ko.observable(true) };
// ...
</script>
{% endhighlight %}

You need to add a set of parentheses after the `isMessageHidden` like so.

{% highlight html %}
<div data-bind="visible: !isMessageHidden()">
{% endhighlight %}

The key to understanding this is understanding that `isMessageHidden` is not a boolean value, but an observable which stores a boolean value. If we simply refer to this observable by name in our data-binding code, then knockout will infer that we want to retrieve the stored value. However, if we wish to refer to the stored value within a statement, such as `!isMessageHidden()`, then the parentheses are required in order to indicate that we want the stored value and not the observable itself. This tripped me up a few times when I was learning Knockout. Hope this helps somebody!

