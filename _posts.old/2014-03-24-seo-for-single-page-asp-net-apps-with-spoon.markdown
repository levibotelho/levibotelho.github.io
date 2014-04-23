---
layout: post
status: publish
published: true
title: SEO for Single Page ASP.NET Apps with Spoon
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! 'Search engine optimisation is one of the most important aspects of any
  public web application. However single page application developers face a significant
  challenge in exposing their content to search engines and web crawlers.


  The problem is that the concept of a web page is not necessarily the same for a
  web server as it is for a user. While the user of an SPA could navigate through
  an entire site and be convinced that it was made up of countless individual web
  pages, a crawler attempting to crawl such a site would only ever end up downloading
  and analysing a single HTML file. To make matters worse, this file would without
  a doubt contain very little content, as most of the content that makes up an SPA
  is injected at runtime using JavaScript.


  The result of all this is that while SPAs provide users with an enjoyable user experience,
  web crawlers aren''t adapted to understand how SPAs are built and what kinds of
  content they contain.


  <h2>Google''s solution</h2>


  Luckily for us, Google has come up with a solution to this problem. The solution
  consists of a protocol which allows sites to tell crawlers about their dynamic content.
  <a href="https://developers.google.com/webmasters/ajax-crawling/docs/specification"
  title="Ajax Crawling - Full Specification">The full text of the protocol can be
  found here</a>, but the general idea is as follows. Normally SPAs use hash fragments
  to denote different pages in an app, like this.

'
wordpress_id: 4941
wordpress_url: http://www.levibotelho.com/?p=4941
date: !binary |-
  MjAxNC0wMy0yNCAyMzoyNjo0MCArMDEwMA==
date_gmt: !binary |-
  MjAxNC0wMy0yNCAyMjoyNjo0MCArMDEwMA==
categories:
- ASP.NET
- Front-end Web
- Open Source
tags:
- asp.net
- angular.js
- seo
- google
- spoon
comments: []
---
<p>Search engine optimisation is one of the most important aspects of any public web application. However single page application developers face a significant challenge in exposing their content to search engines and web crawlers.</p>
<p>The problem is that the concept of a web page is not necessarily the same for a web server as it is for a user. While the user of an SPA could navigate through an entire site and be convinced that it was made up of countless individual web pages, a crawler attempting to crawl such a site would only ever end up downloading and analysing a single HTML file. To make matters worse, this file would without a doubt contain very little content, as most of the content that makes up an SPA is injected at runtime using JavaScript.</p>
<p>The result of all this is that while SPAs provide users with an enjoyable user experience, web crawlers aren't adapted to understand how SPAs are built and what kinds of content they contain.</p>
<h2>Google's solution</h2>
<p>Luckily for us, Google has come up with a solution to this problem. The solution consists of a protocol which allows sites to tell crawlers about their dynamic content. <a href="https://developers.google.com/webmasters/ajax-crawling/docs/specification" title="Ajax Crawling - Full Specification">The full text of the protocol can be found here</a>, but the general idea is as follows. Normally SPAs use hash fragments to denote different pages in an app, like this.<br />
<a id="more"></a><a id="more-4941"></a><br />
<code>http://www.mysite.com/#/contact</code></p>
<p>The protocol states that a hashbang should be used instead of a hash, like so.</p>
<p><code>http://www.mysite.com/#!/contact</code></p>
<p>Now, whenever a crawler sees a link to a page containing a hashbang fragment such as this one, it makes a request to the site passing in the fragment as a query parameter.</p>
<p><code>http://www.mysite.com?_escaped_fragment_=/contact</code></p>
<p>When the server sees such a request come in, it can serve up a static HTML page containing all the content that is dynamically presented in the actual one.</p>
<h2>This can be a pain...</h2>
<p>While this hashbang solution is quite good, it has one annoying consequence for web developers. In order to use this protocol, developers need to maintain static copies of all their dynamic pages which are publicly exposed to web crawlers. Keeping this content up to date as a site evolves is both repetitive and error prone. &quot;There must be a better way!&quot;</p>
<h2>There is!</h2>
<p>This is the problem that I have attempted to solve with Spoon. Spoon is a class library which takes snapshots of your dynamic web pages and serves them up when <code>_escaped_fragment_</code> requests come in.</p>
<h2>How to use Spoon</h2>
<p>Using Spoon is really easy. Get the NuGet package (<code>PM&gt; Install-Package Spoon</code>) and hook the following code into to your site's <code>Application_Start()</code> method.</p>
<p>[csharp]<br />
// Dictionary mapping escaped fragments to page URLs.<br />
// This may be generated from a Sitemap.<br />
var escapedFragmentUrlPairs = new Dictionary&lt;string, string&gt;<br />
{<br />
    { &quot;/home&quot;, &quot;http://www.example.com/!#/home&quot; },<br />
    { &quot;/about&quot;, &quot;http://www.example.com/!#/about&quot; },<br />
    { &quot;/contact&quot;, &quot;http://www.example.com/!#/contact&quot; },<br />
};</p>
<p>var snapshotsPath = HostingEnvironment.MapPath(&quot;[Path]&quot;);<br />
var snapshotsDirectory = new DirectoryInfo(snapshotsPath);<br />
foreach (var file in snapshotsDirectory.EnumerateFiles())<br />
    file.Delete();</p>
<p>SnapshotManager<br />
	.InitializeAsync(escapedFragmentUrlPairs, snapshotsPath)<br />
	.Wait();<br />
[/csharp]</p>
<p>In this example, <code>escapedFragmentUrlPairs</code> is a dictionary mapping escaped fragment values to URLs on your site. This essentially serves to tell Spoon that for a given hash fragment you want a snapshot of the corresponding page to be served to the web crawler. If you have a Sitemap defined for your site, this dictionary could easily be auto-generated from it. The other variable in the sample, <code>snapshotsPath</code>, is simply the path to the folder where Spoon will store the snapshots it creates.</p>
<p>To serve up snapshots you'll need to modify your main action method to handle the <code>_escaped_fragment_</code> parameter.</p>
<p>[csharp]<br />
public async Task&lt;ActionResult&gt; Index(string _escaped_fragment_)<br />
{<br />
    if (_escaped_fragment_ != null)<br />
    {<br />
        string path;<br />
        try<br />
        {<br />
            path = await SnapshotManager<br />
				.GetSnapshotUrlAsync(_escaped_fragment_);<br />
        }<br />
        catch (ArgumentException)<br />
        {<br />
            // Failure logic here.<br />
        }<br />
        return File(path, &quot;text/html&quot;);<br />
    }</p>
<p>    return View();<br />
}<br />
[/csharp]</p>
<p>That's all there is to it. When an <code>_escaped_fragment_</code> is passed to the method, Spoon will look to see if that fragment has been registered. If it has, Spoon will return to you the path to the snapshot file that you can serve with ASP.NET's <code>File()</code> method. If the fragment hasn't been registered, an <code>ArgumentException</code> is thrown. It is up to you to catch this exception and do what you please. Be careful! If a snapshot cannot be served for a given page, then web crawlers won't be able to tell what that page contains and that page will risk not being indexed correctly. At the very least the <code>catch</code> should contain some form of logic to alert you, the developer, that a given <code>_escaped_fragment_</code> has gone unserved in your web app.</p>
<h2>The code</h2>
<p>Spoon is fully open source, licensed under the MIT license. If you're interested, the source code is available <a href="https://github.com/LeviBotelho/spoon">here on GitHub</a>. If you happen to use Spoon and find any bugs or have any feature requests, please <a href="https://github.com/LeviBotelho/spoon/issues">create an issue on the GitHub page</a>. Good luck, and happy SEO-ing!</p>
