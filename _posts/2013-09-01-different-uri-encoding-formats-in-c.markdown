---
layout: post
status: publish
published: true
title: Different URI encoding formats in C#
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
wordpress_id: 2222
wordpress_url: http://www.levibotelho.com/?p=2222
date: !binary |-
  MjAxMy0wOS0wMSAxNDo1MTowMCArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wOS0wMSAxMjo1MTowMCArMDIwMA==
categories:
- C#
- ASP.NET
tags:
- c#
- asp.net
- encoding
comments: []
---
<p>Ever need to encode a string of text to make it web-compliant in some way but didn’t know which encoding function to use? I recently stumbled across a StackOverflow post where a user has provided a sample table of the different encodings provided by the HttpUtility and Uri classes in C# and thought I would share it here. As an example, this is what is provided for the greater-than sign (&gt;):</p>
<table style="padding: 0.5em; border: 1px solid black">
<tbody>
<tr>
<td>Unencoded:</td>
<td>&gt;</td>
</tr>
<tr>
<td>HttpUtility.UrlEncode:</td>
<td>%3e</td>
</tr>
<tr>
<td>HttpUtility.UrlEncodeUnicode:</td>
<td>%3e</td>
</tr>
<tr>
<td>HttpUtility.UrlPathEncode:</td>
<td>&gt;</td>
</tr>
<tr>
<td>Uri.EscapeDataString:</td>
<td>%3E</td>
</tr>
<tr>
<td>Uri.EscapeUriString:</td>
<td>%3E</td>
</tr>
<tr>
<td>HttpUtility.HtmlEncode:</td>
<td>&amp;gt;</td>
</tr>
<tr>
<td>HttpUtility.HtmlAttributeEncode:</td>
<td>&gt;</td>
</tr>
<tr>
<td>Uri.HexEscape:</td>
<td>%3E</td>
</tr>
</tbody>
</table>
<p>If you do any kind of web or api development you’ll already see how useful this can be. Here is the link to the full table: <a title="Stack Overflow - URL Encoding in C#" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://stackoverflow.com']);" href="http://stackoverflow.com/a/11236038/1068266" target="_blank">http://stackoverflow.com/a/11236038/1068266</a></p>
<p>Enjoy!</p>
