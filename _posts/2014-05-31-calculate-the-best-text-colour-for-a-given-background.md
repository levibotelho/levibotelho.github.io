---
layout: post
title: Calculate the best text colour for a given background
category: Development
tags: [c#, design, ui]
comments: true
share: true
---

At one point or another you may find yourself in the situation of having to display text on a background whose colour is chosen by the user of your application. Should this happen to you, one of the first problems that will come to mind is how to ensure that the text always remains readable. Well, there is an easy way to do this. With a little math, you can simply go about calculating the relative brightness of the chosen colour and subsequently determine whether white or black text will provide optimum contrast. Sound easy? It is! Let's look at how it's done.

## Calculating brightness

Every colour in the RGB spectrum can be imagined as a point in a three-dimensional space, where each dimension represents the amount of red, green, or blue in the colour. In this system, a point located at the origin would contain no colour and therefore be black, and a point located at (255, 255, 255) would have full values for all three colours and therefore be white.

We can use this visualization to obtain the relative brightness of a given colour. The greater the distance between a colour's point and the origin, the brighter the colour. Obtaining this distance isn't difficult; it just requires a small amount of geometry.

{% highlight csharp %}
static double CalculateBrightness(Color color)
{
    return Math.Sqrt(Math.Pow(color.R, 2) + Math.Pow(color.G, 2) + Math.Pow(color.B, 2));
}
{% endhighlight %}

This method provides us with a good starting point, but as it stands it's actually not quite accurate. The reason for this is that each of the three component colours contributes differently to the resulting colour's overall brightness. We therefore need to multiply each component colour value by a coefficient which compensates for this fact. The coefficients that we are going to use come from the [W3C's accessibility guidelines for calculating colour contrast](http://www.w3.org/TR/AERT#color-contrast), and are as follows.

+ Red - 0.299
+ Green - 0.587
+ Blue - 0.114

In addition to compensating for relative brightness, these values also ensure that the value returned from the equation sits conveniently between 0 and 255. We integrate them into our formula as follows.

{% highlight csharp %}
static double ConvertToGrayscaleValue(Color color)
{
    return Math.Sqrt(Math.Pow(color.R, 2) * 0.299 +
                     Math.Pow(color.G, 2) * 0.587 +
                     Math.Pow(color.B, 2) * 0.114);
}
{% endhighlight %}

Excellent. So, now we have a function which returns the brightness of a colour on a scale from 0 to 255, 0 being the darkest and 255 being the lightest. Given this information, we should now be able to decide whether black or white text would be most appropriate. Taking 128 as the midpoint, any colour returning a value greater than 128 should be considered "light" and therefore take black text, and anything less than 128 should be considered "dark" and take white text.

Right?

## Gamma correction

Well actually, no. While our formula for calculating relative brightness is correct, it would be erroneous to use a value of 128/256 as the midpoint between light and dark. The reason for this is that computer monitors apply a technique known as gamma correction to the video signals that they receive from the system. On modern screens that are correctly calibrated, gamma correction involves taking the brightness of a colour, expressed on a scale from 0 to 1, and raising its value to the power of 2.2.

This has an impact on our brightness algorithm, because we don't actually care about how bright a colour is supposed to be. We care about how bright a colour will *appear to be on the user's screen*. A colour which has a  brightness of 140 according to our algorithm will actually be displayed with a brightness of (((140/255)^2.2) * 255) = **68**. This colour should therefore be considered dark and take white text. Not the other way around.

To adapt our algorithm to take gamma correction into account, we simply need to calculate the gamma-corrected midpoint between 0 and 255 and use it instead of 128. This is easy to do. We just need to raise 0.5 to the reciprocal of 2.2 and scale it up to use it on values ranging from 0 to 255. This gives us a new midpoint value of (0.5^(1/2.2) * 255) = **186.08371**

So, given that a colour with a brightness of 186/255 will *appear* to be midway between black and white, we can now state that any colour with a brightness above 186 should be considered light and have black text, and that all other colours should be considered to be dark and have white text. We can incorporate this into our algorithm and create a function which returns the optimum text colour as follows.

{% highlight csharp %}
public static Color GetForegroundColor(Color background)
{
    var luminosity = Math.Sqrt(Math.Pow(background.R, 2) * 0.299 +
							   Math.Pow(background.G, 2) * 0.587 +
							   Math.Pow(background.B, 2) * 0.114);

    return luminosity > 186 ? Colors.Black : Colors.White;
}
{% endhighlight %}