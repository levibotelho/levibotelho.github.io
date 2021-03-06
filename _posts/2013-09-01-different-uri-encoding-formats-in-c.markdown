---
layout: post
title: Different URI encoding formats in C#
category: Development
tags: [c#, asp.net, encoding]
description: A handy guide to all the different URI encoding methods in C#.
comments: true
share: true
redirect_from: "/different-uri-encoding-formats-in-c/"
---
Ever need to encode a string of text to make it web-compliant in some way but didn’t know which encoding function to use? I recently stumbled across a StackOverflow post where a user has provided a sample table of the different encodings provided by the HttpUtility and Uri classes in C# and thought I would share it here. As an example, this is what is provided for the greater-than sign (>):

<table style="padding: 0.5em; border: 1px solid black">
<tbody>
<tr>
<td>Unencoded:</td>
<td>></td>
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
<td>></td>
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
<td>></td>
</tr>
<tr>
<td>Uri.HexEscape:</td>
<td>%3E</td>
</tr>
</tbody>
</table>
If you do any kind of web or api development you’ll already see how useful this can be. Here is the link to the full table: [http://stackoverflow.com/a/11236038/1068266](http://stackoverflow.com/a/11236038/1068266)

Enjoy!

