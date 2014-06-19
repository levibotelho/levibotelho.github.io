---
layout: post
title: !'[C#] Remove diacritics (accents) from a string' 
category: Development
tags: [c#, unicode, encoding]
comments: true
share: true
---

Removing diacritics from a string is a common task that doesn't have a built-in method in the .NET framework. Luckily, it's really easy to write an extension method which will do the job. Here is how it's done.

{% highlight csharp %}
public static string RemoveDiacritics(this string text)
{
    if (string.IsNullOrWhiteSpace(text))
        return text;

    text = text.Normalize(NormalizationForm.FormD);
    var chars = text.Where(c => CharUnicodeInfo.GetUnicodeCategory(c) != UnicodeCategory.NonSpacingMark).ToArray();
    return new string(chars).Normalize(NormalizationForm.FormC);
}
{% endhighlight %}

## How it's done

To understand how this method works, you should have a basic understanding of what Unicode is all about. If your Unicode knowledge is a bit rusty, check out [my post from two weeks ago which covers the essentials](http://www.levibotelho.com/development/unicode-and-utf-what-you-need-to-know/).

So, let's have a look at the algorithm. The first thing that we do is convert the string to Unicode Normalization Form D. What does this mean, exactly? Well, many characters in Unicode actually have multiple numerical representations. For example, the character "ô" can be represented both by the code point `U+00F4` ("Latin small letter o with circumflex"), or by the pair of code points `U+006F 0+0302` ("Latin small letter o" and "combining circumflex accent"). This is done in order to improve compatibility with other character sets.

However while this feature improves compatibility, it complicates string comparisons. If "ô" can be represented two different ways, then we can't simply compare the numerical value of two strings to determine whether they are equal. To solve this problem, Unicode defines several "Normalization Forms", which are sets of rules used to transform syntactically-equivalent strings into numerically-equivalent versions of themselves. Normalization Form D does this in part by taking precomposed characters (such as `U+00F4`) and converting them to their decomposed equivalents (`U+006F 0+0302`).

This decomposition serves us well when trying to remove diacritics from a string. Once a string has been transformed in this way, all we need to do is strip out the code points which represent the diacritical marks, and recompose it afterwards.

We make use of Normalization Form D in our method because it separates diacritical marks from the base character that they modify. With all the characters in our string decomposed in this way, we can simply run through the string and remove all diacritic code points. This leaves behind only the unaccented characters.

So, back to the issue of the diacritics. By converting our input string into Unicode Normalization Form D, we end up decomposing all accented characters into base characters and their accents. With this conversion done, we can then go ahead and run through each code point in the string and remove all diacritics (which are classified as "non spacing marks" in Unicode). All that's left to do is recompose the string and convert it to Normalization Form C, which simply recombines code points representing characters that were decomposed by converting to Normalization Form D.