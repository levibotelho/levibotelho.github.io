---
layout: post
title: Dragging and dropping files not working with Knockout.js
category: Development
tags: [javascript, jquery, html, knockout.js]
comments: true
share: true
---
I recently encountered a problem when trying to implement drag and drop file uploads in a site I'm working on that uses Knockout.js. A simplified reproduction of the problem is as follows:

{% highlight html %}
<div data-bind="event: { drop: acceptDrop }">
    Drop it here.
</div>
<script src="jquery-1.10.2.js"></script>
<script src="knockout-2.3.0.js"></script>
<script>
    var viewModel = function () {
        var self = this;

        self.acceptDrop = function (model, e) {
            var files = e.dataTransfer.files;
            // Do something with the files...
        };
    };

    ko.applyBindings(new viewModel());
</script>
{% endhighlight %}

The problem that I encountered was that when I attempted to retrieve the files from the event object (line 15), e.dataTransfer was undefined.

The problem ended up being that when using Knockout with jQuery, the event object sent to the view model function is a jQuery event object. While jQuery event objects are helpful in as much as they contain additional information not found in standard event objects, they do not expose certain case-specific event members such as dataTransfer. In order to get to dataTransfer, we need to access it via the jQuery event's “originalEvent” property like so.

{% highlight javascript %}
var files = e.originalEvent.dataTransfer.files;
{% endhighlight %}

This returns the original event object, which contains the dataTransfer property that will allow us to get to our files.

<hr />
*Note: If you aren’t using jQuery Knockout sends up standard event objects so you won’t encounter this problem. That said, if you’re using Knockout chances are you’re probably using jQuery as well.*

