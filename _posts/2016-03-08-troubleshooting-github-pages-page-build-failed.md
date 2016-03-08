---
layout: post
title: Troubleshooting GitHub Pages “Page build failed”
category: Miscellaneous
tags: [github-pages]
description: When GitHub Pages just refuses to build your site, there may be an unlikely culprit.
comments: true
share: true
---

When GitHub Pages refuses to build your site, you’ll sometimes get a detailed error message pointing you right to the source of the problem. And other times, you’ll simply get a “Page build failed” message with nothing more to help you out.

If this happens to you, your first course of action should be to check out [GitHub’s troubleshooting page](https://help.github.com/articles/troubleshooting-github-pages-build-failures/), but should that fail there is something else you should take a look at: the encoding of your markdown files.

If you’ve recently added a page and happened to save the markdown file with Notepad at one point or another, chances are the file will be encoded in ANSI. GitHub pages doesn’t like this very much, but doesn’t seem to want to talk about it either. Change the encoding to UTF-8, push, and your site will build as expected.