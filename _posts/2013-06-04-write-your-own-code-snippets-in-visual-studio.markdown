---
layout: post
title: Write your own code snippets in Visual Studio
category: Visual Studio
tags: [visual studio, snippets]
comments: true
share: true
---
Code snippets are a great feature in Visual Studio that automate the writing of oft-repeated code. By typing a special shortcut followed by two strokes of the tab key, you get a preformatted template for a commonly-used code segment. (Try it with *prop* if you don’t know what I’m talking about.)
While there are many snippets available in Visual Studio by default, it’s also easy to create your own. Here’s how:
Define the code to template
For the purposes of this demo, I’m going to create a template to return the result of a conditional ternary operation. The first step is to define what the snippet is going to look like:
<a id="more"></a><a id="more-952"></a>
{% highlight csharp %}
return ([something Boolean]) ? [if true] : [if false];
{% endhighlight %}

# Write the snippet

## The basics

Snippets are written in XML. The basic format is as follows:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet">
    <CodeSnippet Format="1.0.0">
        <Header>
            <Title></Title>
            <Author></Author>
            <Description></Description>
            <Shortcut></Shortcut>
        </Header>
        <Snippet>
            <Declarations>
                <!-- This section is optional. -->
            </Declarations>
            <Code Language="csharp">
                <![CDATA[ CODE GOES HERE ]]>
            </Code>
        </Snippet>
    </CodeSnippet>
</CodeSnippets>
{% endhighlight %}

This is all pretty self-explanatory. The “Declarations” section in this template isn’t strictly mandatory, but often required. It is there where you can define placeholders to replace in the template defined in the “code” section of the document. Let’s fill this in:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet">
    <CodeSnippet Format="1.0.0">
        <Header>
            <Title>Return Ternary Conditional</Title>
            <Author>Levi Botelho</Author>
            <Description>
                Returns a value from the result of a given ternary conditional operation.
            </Description>
            <Shortcut>rett</Shortcut>
        </Header>
        <Snippet>
            <Declarations>
                <!-- This section is optional. -->
            </Declarations>
            <Code Language="csharp">
                <![CDATA[return ([something Boolean]) ? [if true] : [if false];]]>
            </Code>
        </Snippet>
    </CodeSnippet>
</CodeSnippets>
{% endhighlight %}

## Variables

So far the snippet looks pretty good. The one thing that isn’t quite right though are the placeholders in the actual code. If we imported this snippet into VS right now it would indeed work, but would output exactly what is found in the CDATA tag onto the page. Instead, we want to make it easy for the end user (me!) to replace what is in the square brackets with real values. This is where that “Declarations” section comes in.
Two types of declarations can be made: literals and objects.
To declare a literal, insert the following code inside the declarations element of the document:

{% highlight xml %}
<ID>[Used to reference the literal in the snippet]</ID>
<ToolTip>[Appears as a tooltip in the IDE]</ToolTip>
<Default>[The default value that appears in the snippet.]</Default>
{% endhighlight %}

The syntax to declare an object is the same, but with an added “Type” tag, as follows:

{% highlight xml %}
<ID>[Used to reference the literal in the snippet]</ID>
<Type>[The type (namespaces included) of the object to insert]</Type>
<ToolTip>[Appears as a tooltip in the IDE]</ToolTip>
<Default>[The default value that appears in the snippet.]</Default>
{% endhighlight %}

To reference a declaration, simply insert the ID anywhere in the snippet, surrounded by dollar signs. For our snippet we need to provide declarations for our three placeholders. I’ll go ahead and create the three literal placeholders.

{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet">
    <CodeSnippet Format="1.0.0">
        <Header>
            <Title>Return Ternary Conditional</Title>
            <Author>Levi Botelho</Author>
            <Description>
                Returns a value from the result of a given ternary conditional operation.
            </Description>
            <Shortcut>rett</Shortcut>
        </Header>
        <Snippet>
            <Declarations>
                <Literal>
                    <ID>Condition</ID>
                    <ToolTip>The condition to validate.</ToolTip>
                    <Default>Condition</Default>
                </Literal>
                <Literal>
                    <ID>True</ID>
                    <ToolTip>The return value if true.</ToolTip>
                    <Default>IfTrue</Default>
                </Literal>
                <Literal>
                    <ID>False</ID>
                    <ToolTip>The return value if false.</ToolTip>
                    <Default>IfFalse</Default>
                </Literal>
            </Declarations>
            <Code Language="csharp">
                <![CDATA[return ($Condition$) ? $True$ : $False$;]]>
            </Code>
        </Snippet>
    </CodeSnippet>
</CodeSnippets>
{% endhighlight %}

# Installation

Now that our snippet is written, we can go about importing it into Visual Studio. Save your snippet file with a .snippet extension, and then open the Code Snippets Manager in VS (**TOOLS -> Code Snippets Manager**). Click the **Import** button, locate your snippet and import it into the `My Code Snippets` folder. If you pass this step that means that your snippet is valid code, and will now appear in the list with all the others. Open a code page and type the shortcut followed by <kbd>tab</kbd> <kbd>tab</kbd>, and you should see your new snippet appear on the page.

{% highlight csharp %}
return (Condition) ? IfTrue : IfFalse;
{% endhighlight %}

You're done!

