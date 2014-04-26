---
layout: post
title: SEO for Single Page ASP.NET Apps with Spoon
category: Open Source
tags: [asp.net, angular.js, seo, google, spoon, open source]
comments: true
share: true
---
Search engine optimisation is one of the most important aspects of any public web application. However single page application developers face a significant challenge in exposing their content to search engines and web crawlers.

The problem is that the concept of a web page is not necessarily the same for a web server as it is for a user. While the user of an SPA could navigate through an entire site and be convinced that it was made up of countless individual web pages, a crawler attempting to crawl such a site would only ever end up downloading and analysing a single HTML file. To make matters worse, this file would without a doubt contain very little content, as most of the content that makes up an SPA is injected at runtime using JavaScript.

The result of all this is that while SPAs provide users with an enjoyable user experience, web crawlers aren't adapted to understand how SPAs are built and what kinds of content they contain.

## Google's solution

Luckily for us, Google has come up with a solution to this problem. The solution consists of a protocol which allows sites to tell crawlers about their dynamic content. [The full text of the protocol can be found here](https://developers.google.com/webmasters/ajax-crawling/docs/specification), but the general idea is as follows. Normally SPAs use hash fragments to denote different pages in an app, like this.

`http://www.mysite.com/#/contact`

The protocol states that a hashbang should be used instead of a hash, like so.

`http://www.mysite.com/#!/contact`

Now, whenever a crawler sees a link to a page containing a hashbang fragment such as this one, it makes a request to the site passing in the fragment as a query parameter.

`http://www.mysite.com?_escaped_fragment_=/contact`

When the server sees such a request come in, it can serve up a static HTML page containing all the content that is dynamically presented in the actual one.

## This can be a pain...

While this hashbang solution is quite good, it has one annoying consequence for web developers. In order to use this protocol, developers need to maintain static copies of all their dynamic pages which are publicly exposed to web crawlers. Keeping this content up to date as a site evolves is both repetitive and error prone. "There must be a better way!"

## There is!

This is the problem that I have attempted to solve with Spoon. Spoon is a class library which takes snapshots of your dynamic web pages and serves them up when `_escaped_fragment_` requests come in.

## How to use Spoon

Using Spoon is really easy. Get the NuGet package (`PM> Install-Package Spoon`) and hook the following code into to your site's `Application_Start()` method.

{% highlight csharp %}
// Dictionary mapping escaped fragments to page URLs.
// This may be generated from a Sitemap.
var escapedFragmentUrlPairs = new Dictionary<string, string>
{
    { "/home", "http://www.example.com/!#/home" },
    { "/about", "http://www.example.com/!#/about" },
    { "/contact", "http://www.example.com/!#/contact" },
};

var snapshotsPath = HostingEnvironment.MapPath("[Path]");
var snapshotsDirectory = new DirectoryInfo(snapshotsPath);
foreach (var file in snapshotsDirectory.EnumerateFiles())
    file.Delete();

SnapshotManager
	.InitializeAsync(escapedFragmentUrlPairs, snapshotsPath)
	.Wait();
{% endhighlight %}

In this example, `escapedFragmentUrlPairs` is a dictionary mapping escaped fragment values to URLs on your site. This essentially serves to tell Spoon that for a given hash fragment you want a snapshot of the corresponding page to be served to the web crawler. If you have a Sitemap defined for your site, this dictionary could easily be auto-generated from it. The other variable in the sample, `snapshotsPath`, is simply the path to the folder where Spoon will store the snapshots it creates.

To serve up snapshots you'll need to modify your main action method to handle the `_escaped_fragment_` parameter.

{% highlight csharp %}
public async Task<ActionResult> Index(string _escaped_fragment_)
{
    if (_escaped_fragment_ != null)
    {
        string path;
        try
        {
            path = await SnapshotManager
				.GetSnapshotUrlAsync(_escaped_fragment_);
        }
        catch (ArgumentException)
        {
            // Failure logic here.
        }
        return File(path, "text/html");
    }

    return View();
}
{% endhighlight %}

That's all there is to it. When an `_escaped_fragment_` is passed to the method, Spoon will look to see if that fragment has been registered. If it has, Spoon will return to you the path to the snapshot file that you can serve with ASP.NET's `File()` method. If the fragment hasn't been registered, an `ArgumentException` is thrown. It is up to you to catch this exception and do what you please. Be careful! If a snapshot cannot be served for a given page, then web crawlers won't be able to tell what that page contains and that page will risk not being indexed correctly. At the very least the `catch` should contain some form of logic to alert you, the developer, that a given `_escaped_fragment_` has gone unserved in your web app.

## The code

Spoon is fully open source, licensed under the MIT license. If you're interested, the source code is available [create an issue on the GitHub page](https://github.com/LeviBotelho/spoon/issues). Good luck, and happy SEO-ing!

