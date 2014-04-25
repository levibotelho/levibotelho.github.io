---
layout: post
title: Implementing a pop-in window in a web page
category: [Front-end Web]
tags: [css, javascript, html]
comments: true
share: true
---
Implementing a pop-in window in a web page is a lot easier than most people think. Here's how to do it:

# 1. Grey-out the screen

The first step is to grey-out the screen. We do this by filling the viewport with a semi-transparent div element. To do this, apply the following CSS class to a div element placed anywhere in your page. This element will become the container of your pop-in window.

{% highlight css %}
.popinBackground {
    background-color: RGBA(0, 0, 0, 0.5);
    height: 100%;
    left: 0;
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 10;
}
{% endhighlight %}
<a id="more"></a><a id="more-192"></a>
position: fixed, left: 0, top: 0 and the 100% width and height ensures that the div constantly covers the entire viewport. In order to define opacity we use an rgba value of (0, 0, 0, 0.5) as the background colour, which defines it as half-transparent black. Finally, we apply a relatively high z-index to ensure that the div to which the class is applied will always be on top of the page.

<blockquote>Personal note: It drives me crazy when I see that a developer has set the z-index of a given element to 10,000 just to ensure that it is on top of everything. Be reasonable with your z-index values... A z-index of 10,000 is no more effective than a z-index of 2 on 95% of web pages.

</blockquote>
>

# 2. Insert a centered white content box

We now need to insert a centered white box into the covering div that will hold our pop-in's content. As most web developers are aware, vertically-centering an element is a painful ordeal if you don't know the tricks to make it happen. I am going to use the “display: table-cell” method of centering, as it doesn’t require knowledge of the window’s dimensions and is more browser compliant than a solution implementing CSS3’s calc function. (See my previous blog post if you haven't heard of calc!)

This is what the pop-in's HTML looks like with the content box added to it:

{% highlight html %}
<div id="MyPopin" class="popinBackground">
    <div class="popinOuterContainer">
        <div class="popinInnerContainer">
            <div class="popinContent">
                Here is some content for the pop-in!
            </div>
        </div>
    </div>
</div>
{% endhighlight %}

And the corresponding CSS:

{% highlight css %}
.popinBackground {
    background-color: RGBA(0, 0, 0, 0.5);
    height: 100%;
    left: 0;
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 10;
}

.popinOuterContainer {
    display: table;
    height: 100%;
    margin: 0 auto 0 auto;
}

.popinInnerContainer {
    display: table-cell;
    vertical-align: middle;
}

.popinContent {
    background-color: white;
    height: auto;
    width: auto;
}
{% endhighlight %}

The content window is horizontally centered using the standard auto-margins technique, and as mentioned the window is vertically-centred using the display: table-cell method. If you haven’t seen this method of vertical centering before, all that is happening is that the display: table and display: table-cell values are making it such that vertical-align: middle vertically aligns elements to the middle of the page (which isn’t easy as most web developers are well aware). The width and height are auto values, which means that this content box will shrink to the size of its contents. We’re almost done!

# 3. Toggle the box's appearance

This is the final step required for the implementation of our pop-in window. Here is a basic function which does the job:

{% highlight javascript %}
function TogglePopin(id) {
    var popin = document.getElementById(id);
    if (popin.style.display == "none") {
        popin.style.display = "block";
    } else {
        popin.style.display = "none";
    }
}
{% endhighlight %}

You can now call this function both from a button on the page and a button in the pop-in, passing in the ID of the popin background div to show or hide the pop-in window as you please. By passing the ID to the function we are able to reuse it for multiple pop-in windows on the same page if we so choose.

