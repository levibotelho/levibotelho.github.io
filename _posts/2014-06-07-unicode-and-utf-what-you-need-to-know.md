---
layout: post
title: Unicode and UTF - What you need to know
category: Development
tags: [c#, unicode, encoding]
description: It's hard to overstate the importance of Unicode in modern computing. Here's a quick introduction to Unicode and UTF, written for developers who want to get up to speed on the basics.
comments: true
share: true
---

## What is Unicode?

> "Unicode provides a unique number for every character, no matter what the platform, no matter what the program, no matter what the language." - Unicode, Inc.

That really sums it all up.

## How does it work?

Unicode assigns one of 1,114,112 numbers, known as code points, to each character used in most of the world's writing systems. Contrary to what many people think, Unicode does not actually define how these values are encoded. It simply provides a universally-accepted mapping between code points and characters. The actual details of how the code points are encoded into binary data are handled by other standards, such as UTF.

## UTF

UTF is a family of encoding schemes which are capable of representing all of the code points defined in the Unicode standard. The three main variants of UTF that are used today are UTF-8, UTF-16 and UTF-32. 8, 16, and 32 each represent the size in bits of the "code unit" that is used to encode each code point.

### UTF-32

UTF-32 is the simplest scheme out of the three. Each code point is encoded as a 32-bit value. While this can prove to be convenient, it's often a very wasteful way to store data. Most characters used in English writing can be represented using only 7 bits of data, meaning that UTF-32 wastes quite a lot of space when storing text primarily comprised of these characters. Due to its inefficiency, UTF-32 isn't very widely used.

### UTF-8

UTF-8 is the ideological opposite of UTF-32. Each code block in UTF-8 is only 8 bits long. To support code points that require more than 8 bits of storage, UTF-8 defines a standard for chaining code blocks together. Each code block is prefixed by one or more control bits which indicate the position of the block in the chain, as well as the length of the chain in the case of the first block. For code points requiring 7 bits of data or less, UTF-8 uses only a single code block, with a "0" used as the control bit. This has two advantages. Firstly, it is extremely efficient to store English language strings in UTF-8. And second, UTF-8 is implicitly backwards-compatible with ASCII, another common character encoding standard which predates Unicode.

The control bits used to make UTF-8 Unicode-compliant also contribute to its downside: Code points requiring more than 21 bits of data end up being represented by five, or even six code blocks chained together. This means that for certain languages, such as Thai, UTF-8 can actually be less efficient than UTF-32. Despite this drawback, UTF-8 is the most popular encoding scheme used on the web today, and is increasingly being used as the default in many different applications.

### UTF-16

UTF-16, as one would expect, is something of a compromise between UTF-8 and UTF-32. UTF-16's code blocks are 16 bits long. Code points are either represented by a single code block, or two that are chained together in what is known as a "surrogate pair". Surrogate pairs, like chained code blocks in UTF-8, require a number of control bits to indicate that the pair represents a single code point. However, unlike UTF-8, the cost of the control bits never makes UTF-16 more expensive than UTF-32.

## Why this is important
As developers, we don't need to know the Unicode standard, or any of the encoding schemes for that matter, like the backs of our own hands. However, understanding how characters are encoded is essential for developing quality software, and even more important when developing for those who do not speak English.

In addition, knowing how different systems encode characters can prove to be very helpful in certain situations. As an example, C# uses UTF-16 to encode its strings. However calling `String.Length` on a C# string returns not the number of characters in the string, but the number of 16-bit code blocks. If you string happens to contain characters represented by surrogate pairs, the string length as returned by the property may not be what you expect. Understanding the fundamentals of character encoding will not only will help you tackle problems such as this one should they arise, but hopefully prevent them from occurring in the first place.