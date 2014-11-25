---
layout: post
title: How to crop/resize images in C#
category: Development
tags: [c#, image]
description: A simple method that lets you crop, stretch, and shrink images in C#.
comments: true
share: true
---
I remember taking quite some time figuring out how to crop an image the first time that I had to do it. The .NET Framework contains a *lot* of methods and overloads that you can use to manipulate images, and finding the right one that meets your needs can prove challenging. Here, I present a simple method that you can use to do basic image cropping and resizing without any hassle.

{% highlight csharp %}
	static Bitmap CropImage(Image originalImage, Rectangle sourceRectangle,
		Rectangle? destinationRectangle = null)
	{
		if (destinationRectangle == null)
		{
			destinationRectangle =
				new Rectangle(Point.Empty, sourceRectangle.Size);
		}

		var croppedImage = new Bitmap(destinationRectangle.Value.Width,
			destinationRectangle.Value.Height);
		using (var graphics = Graphics.FromImage(croppedImage))
		{
			graphics.DrawImage(originalImage, destinationRectangle.Value,
				sourceRectangle, GraphicsUnit.Pixel);
		}

		return croppedImage;
	}
{% endhighlight %}

The first argument is the source image, the second defines the area to crop, and the third optionally specifies the size of the target image. The third argument is only necessary if you would like to scale your image in any way. If you just want a straight-up crop, leave it empty!