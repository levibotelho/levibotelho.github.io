---
layout: post
status: publish
published: true
title: Make WebAPI Return JSON to an AJAX Request
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "If you make an AJAX call to a WebAPI service using an XmlHttpRequest object,
  you’ll notice that the data returned to you is formatted as XML. This is because
  the “Accept” header in a typical XmlHttpRequest call looks something like this:\r\n\r\n[sourcecode
  language=\"text\"]\r\nAccept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8\r\n[/sourcecode]\r\n\r\nThe
  Accept header plays a key role in a procedure known as “content negotiation”. Content
  negotiation is the process by which a client and a server decide in which media
  type to transmit information. The “Accept” header, sent to the server from the client,
  indicates the types in which it is willing to receive data. This particular header
  states that HTML or XHTML is preferred, followed by XML, followed by anything else*.
  As WebAPI supports XML and JSON out of the box, it will by default return data to
  this request in XML, which is the preferred format of the two.\r\n\r\nBut what if
  we want JSON?\r\n"
wordpress_id: 172
wordpress_url: http://levibotelho.azurewebsites.net/?p=172
date: !binary |-
  MjAxMy0wNC0xNyAxMDozNzozMyArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODozNzozMyArMDIwMA==
categories:
- ASP.NET
tags:
- asp.net
- http
- ajax
- javascript
- json
- webapi
- jquery
comments: []
---
<p>If you make an AJAX call to a WebAPI service using an XmlHttpRequest object, you’ll notice that the data returned to you is formatted as XML. This is because the “Accept” header in a typical XmlHttpRequest call looks something like this:</p>
<p>[sourcecode language="text"]<br />
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8<br />
[/sourcecode]</p>
<p>The Accept header plays a key role in a procedure known as “content negotiation”. Content negotiation is the process by which a client and a server decide in which media type to transmit information. The “Accept” header, sent to the server from the client, indicates the types in which it is willing to receive data. This particular header states that HTML or XHTML is preferred, followed by XML, followed by anything else*. As WebAPI supports XML and JSON out of the box, it will by default return data to this request in XML, which is the preferred format of the two.</p>
<p>But what if we want JSON?<br />
<a id="more"></a><a id="more-172"></a><br />
When working in JavaScript, JSON is often preferred to XML due to the ease at which JSON can be parsed into working JavaScript objects. In order to tell WebAPI to return JSON, we simply need to modify the “Accept” header of our HTTP request before it is sent. We do this as follows:</p>
<p>[sourcecode language="javascript"]<br />
var request = new XMLHttpRequest();<br />
request.onreadystatechange = MyCallbackFunction;<br />
request.open(&quot;GET&quot;, &quot;http://mywebapi.com/api/MyService&quot;);<br />
request.setRequestHeader(&quot;Accept&quot;, &quot;application/json&quot;);<br />
request.send();<br />
[/sourcecode]</p>
<p>WebAPI will now send us JSON data in response to our calls. This data can then be parsed into JavaScript objects using the JSON.Parse() method.</p>
<hr />
<p><em>* The “q” value (known as the “relative quality factor”) of the XML and “any” types in this header provides a means of prioritising which types to serve to the calling client. Types with no associated value are served first, followed by the rest in descending order. <a title="W3 - Header Field Definition" href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html" target="_blank">You can read more about the Accept header and the relative quality factor here</a>.</em></p>
