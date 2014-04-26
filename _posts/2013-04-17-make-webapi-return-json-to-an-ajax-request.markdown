---
layout: post
title: Make WebAPI Return JSON to an AJAX Request
category: ASP.NET
tags: [asp.net, http, ajax, javascript, json, webapi, jquery]
comments: true
share: true
---
If you make an AJAX call to a WebAPI service using an XmlHttpRequest object, you’ll notice that the data returned to you is formatted as XML. This is because the “Accept” header in a typical XmlHttpRequest call looks something like this:

{% highlight text %}
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
{% endhighlight %}

The Accept header plays a key role in a procedure known as “content negotiation”. Content negotiation is the process by which a client and a server decide in which media type to transmit information. The “Accept” header, sent to the server from the client, indicates the types in which it is willing to receive data. This particular header states that HTML or XHTML is preferred, followed by XML, followed by anything else*. As WebAPI supports XML and JSON out of the box, it will by default return data to this request in XML, which is the preferred format of the two.

But what if we want JSON?
<a id="more"></a><a id="more-172"></a>
When working in JavaScript, JSON is often preferred to XML due to the ease at which JSON can be parsed into working JavaScript objects. In order to tell WebAPI to return JSON, we simply need to modify the “Accept” header of our HTTP request before it is sent. We do this as follows:

{% highlight javascript %}
var request = new XMLHttpRequest();
request.onreadystatechange = MyCallbackFunction;
request.open("GET", "http://mywebapi.com/api/MyService");
request.setRequestHeader("Accept", "application/json");
request.send();
{% endhighlight %}

WebAPI will now send us JSON data in response to our calls. This data can then be parsed into JavaScript objects using the JSON.Parse() method.

<hr />
* The “q” value (known as the “relative quality factor”) of the XML and “any” types in this header provides a means of prioritising which types to serve to the calling client. Types with no associated value are served first, followed by the rest in descending order. [You can read more about the Accept header and the relative quality factor here](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

