Removing diacritics from a string is a task  one of those things that you have to do from time to time but that doesn't have a go-to method in the .NET Framework.

Luckily, removing diacritics isn't very complicated once you know how to do it. To get straight to the point, here is the code for doing so, written as an extension method. 

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

If you don't care about how it works, then paste it into your code and enjoy! If you want to know *why* this code does what it does, then read on.

## A crash course in Unicode
Character encoding is one of those important concepts in computing where everybody seems to just know enough to get by. However understanding how the above code works will require a bit more than the bare-bones knowledge that most developers have about the subject.

The `char` type in .NET represents a 16-bit Unicode character. 