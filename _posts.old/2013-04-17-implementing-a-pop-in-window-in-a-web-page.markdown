---
layout: post
status: publish
published: true
title: Implementing a pop-in window in a web page
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "Implementing a pop-in window in a web page is a lot easier than most people
  think. Here's how to do it:\r\n<h1>1. Grey-out the screen</h1>\r\nThe first step
  is to grey-out the screen. We do this by filling the viewport with a semi-transparent
  div element. To do this, apply the following CSS class to a div element placed anywhere
  in your page. This element will become the container of your pop-in window.\r\n\r\n[sourcecode
  language=\"css\"]\r\n.popinBackground {\r\n    background-color: RGBA(0, 0, 0, 0.5);\r\n
  \   height: 100%;\r\n    left: 0;\r\n    position: fixed;\r\n    top: 0;\r\n    width:
  100%;\r\n    z-index: 10;\r\n}\r\n[/sourcecode]\r\n"
wordpress_id: 192
wordpress_url: http://levibotelho.azurewebsites.net/?p=192
date: !binary |-
  MjAxMy0wNC0xNyAxMDozODowOSArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODozODowOSArMDIwMA==
categories:
- Front-end Web
tags:
- css
- javascript
- html
comments: []
---
<p>Implementing a pop-in window in a web page is a lot easier than most people think. Here's how to do it:</p>
<h1>1. Grey-out the screen</h1>
<p>The first step is to grey-out the screen. We do this by filling the viewport with a semi-transparent div element. To do this, apply the following CSS class to a div element placed anywhere in your page. This element will become the container of your pop-in window.</p>
<p>[sourcecode language="css"]<br />
.popinBackground {<br />
    background-color: RGBA(0, 0, 0, 0.5);<br />
    height: 100%;<br />
    left: 0;<br />
    position: fixed;<br />
    top: 0;<br />
    width: 100%;<br />
    z-index: 10;<br />
}<br />
[/sourcecode]<br />
<a id="more"></a><a id="more-192"></a><br />
position: fixed, left: 0, top: 0 and the 100% width and height ensures that the div constantly covers the entire viewport. In order to define opacity we use an rgba value of (0, 0, 0, 0.5) as the background colour, which defines it as half-transparent black. Finally, we apply a relatively high z-index to ensure that the div to which the class is applied will always be on top of the page.</p>
<blockquote><p>Personal note: It drives me crazy when I see that a developer has set the z-index of a given element to 10,000 just to ensure that it is on top of everything. Be reasonable with your z-index values... A z-index of 10,000 is no more effective than a z-index of 2 on 95% of web pages.
</p></blockquote>
<p>></p>
<h1>2. Insert a centered white content box</h1>
<p>We now need to insert a centered white box into the covering div that will hold our pop-in's content. As most web developers are aware, vertically-centering an element is a painful ordeal if you don't know the tricks to make it happen. I am going to use the “display: table-cell” method of centering, as it doesn’t require knowledge of the window’s dimensions and is more browser compliant than a solution implementing CSS3’s calc function. (See my previous blog post if you haven't heard of calc!)</p>
<p>This is what the pop-in's HTML looks like with the content box added to it:</p>
<p>[sourcecode language="html"]<br />
&lt;div id=&quot;MyPopin&quot; class=&quot;popinBackground&quot;&gt;<br />
    &lt;div class=&quot;popinOuterContainer&quot;&gt;<br />
        &lt;div class=&quot;popinInnerContainer&quot;&gt;<br />
            &lt;div class=&quot;popinContent&quot;&gt;<br />
                Here is some content for the pop-in!<br />
            &lt;/div&gt;<br />
        &lt;/div&gt;<br />
    &lt;/div&gt;<br />
&lt;/div&gt;<br />
[/sourcecode]</p>
<p>And the corresponding CSS:</p>
<p>[sourcecode language="css"]<br />
.popinBackground {<br />
    background-color: RGBA(0, 0, 0, 0.5);<br />
    height: 100%;<br />
    left: 0;<br />
    position: fixed;<br />
    top: 0;<br />
    width: 100%;<br />
    z-index: 10;<br />
}</p>
<p>.popinOuterContainer {<br />
    display: table;<br />
    height: 100%;<br />
    margin: 0 auto 0 auto;<br />
}</p>
<p>.popinInnerContainer {<br />
    display: table-cell;<br />
    vertical-align: middle;<br />
}</p>
<p>.popinContent {<br />
    background-color: white;<br />
    height: auto;<br />
    width: auto;<br />
}<br />
[/sourcecode]</p>
<p>The content window is horizontally centered using the standard auto-margins technique, and as mentioned the window is vertically-centred using the display: table-cell method. If you haven’t seen this method of vertical centering before, all that is happening is that the display: table and display: table-cell values are making it such that vertical-align: middle vertically aligns elements to the middle of the page (which isn’t easy as most web developers are well aware). The width and height are auto values, which means that this content box will shrink to the size of its contents. We’re almost done!</p>
<h1>3. Toggle the box's appearance</h1>
<p>This is the final step required for the implementation of our pop-in window. Here is a basic function which does the job:</p>
<p>[sourcecode language="javascript"]<br />
function TogglePopin(id) {<br />
    var popin = document.getElementById(id);<br />
    if (popin.style.display == &quot;none&quot;) {<br />
        popin.style.display = &quot;block&quot;;<br />
    } else {<br />
        popin.style.display = &quot;none&quot;;<br />
    }<br />
}<br />
[/sourcecode]</p>
<p>You can now call this function both from a button on the page and a button in the pop-in, passing in the ID of the popin background div to show or hide the pop-in window as you please. By passing the ID to the function we are able to reuse it for multiple pop-in windows on the same page if we so choose.</p>
