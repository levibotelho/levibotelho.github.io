---
layout: post
status: publish
published: true
title: Introduction to Design Time Templating in C#
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "While a good developer will always do their best to write DRY code, cases
  do arise when repetition is inevitable. Mapping one object type to another provides
  a good example of this. While reflection can be used to compare types against one
  another, in high-volume contexts the performance loss that reflection incurs may
  be too significant.\r\n\r\nSo, if we can’t use reflection to map one object to another,
  we need another way to accomplish this. We could write a direct mapper method, mapping
  one type to another manually, but this is a lot of work and requires us to update
  the mapper manually each time the classes change. This is where T4 templates come
  in.\r\n"
wordpress_id: 92
wordpress_url: http://levibotelho.azurewebsites.net/?p=92
date: !binary |-
  MjAxMy0wNC0xNyAwODowNTo1MyArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODowNTo1MyArMDIwMA==
categories:
- Metaprogramming
- C#
tags:
- c#
- t4
comments: []
---
<p>While a good developer will always do their best to write DRY code, cases do arise when repetition is inevitable. Mapping one object type to another provides a good example of this. While reflection can be used to compare types against one another, in high-volume contexts the performance loss that reflection incurs may be too significant.</p>
<p>So, if we can’t use reflection to map one object to another, we need another way to accomplish this. We could write a direct mapper method, mapping one type to another manually, but this is a lot of work and requires us to update the mapper manually each time the classes change. This is where T4 templates come in.<br />
<a id="more"></a><a id="more-92"></a><br />
T4 templates are files which when run generate some sort of textual output, usually code. They look a lot like .aspx files, and function in pretty much the exact same way.</p>
<p>Two types of T4 templates exist: runtime and design time. Runtime templates are executed while the program is running, exactly like .aspx files do, inserting dynamic data into a static skeleton. Design time templates do the same thing, but instead execute pre-compilation. This is an incredibly useful feature, because it allows us to generate C# code that will be error-checked by the compiler just as hand-written code would be. It is this solution that we are going to adopt to map our objects.</p>
<p>For this example we will use two objects, Person1 and Person2 which both look like the following:</p>
<p>[sourcecode language="csharp"]<br />
public class Person1 // (Person2)<br />
{<br />
    public string Name { get; set; }<br />
    public DateTime Birthday { get; set; }<br />
}<br />
[/sourcecode]</p>
<p>Let’s get templating.</p>
<h1>Creating the template</h1>
<p>When you create a T4 template in Visual Studio (they are found in the “General” section of the “Add New Item” window), the file by default contains the following six lines:</p>
<p>[sourcecode language="csharp"]<br />
&lt;#@ template debug=&quot;false&quot; hostspecific=&quot;false&quot; language=&quot;C#&quot; #&gt;<br />
&lt;#@ assembly name=&quot;System.Core&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Linq&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Text&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Collections.Generic&quot; #&gt;<br />
&lt;#@ output extension=&quot;.txt&quot; #&gt;<br />
[/sourcecode]</p>
<p>The “template” declaration specifies whether debugging is enabled, the language in which the template is written, and whether the template is “host specific”. A host specific template allows access to a property called “Host” which enables several actions such as the resolution of filepaths. <a href="http://msdn.microsoft.com/en-us/library/microsoft.visualstudio.texttemplating.itexttemplatingenginehost.aspx">A full list of these actions can be found here</a>.</p>
<p>The “assembly” declaration points to assemblies which are loaded into the template. This is similar to adding a reference in a project.</p>
<p>The “import” statement functions just as a “using” statement does, importing namespaces into the template file. Because Intellisense is currently not supported by default in T4 templates, forgetting to import a namespace is a common error to make when writing a template.</p>
<p>Finally, the output statement specifies what format the outputted file should take. In our case we want this to be “.cs”.</p>
<h1>Writing the template logic</h1>
<p>Let’s start writing. We’ll start by writing a direct mapper in plain old C#, from which we can derive our template.</p>
<p>[sourcecode language="csharp"]<br />
namespace MapperApplication<br />
{<br />
    public static class Mapper<br />
    {<br />
        public static Person2 Map(Person1 person1)<br />
        {<br />
            return new Person2<br />
            {<br />
                Name = person1.Name,<br />
                Birthday = person1.Birthday<br />
            };<br />
        }<br />
    }<br />
}<br />
[/sourcecode]</p>
<p>Now let’s start the template. All we are really trying to achieve for the sake of this example is to replace the two innermost lines of code with calculated output from our template. The rest will remain the same. Let’s start by writing the C# to produce these lines of code given the two types:</p>
<p>[sourcecode language="csharp"]<br />
const string person1Name = &quot;person1&quot;;<br />
const string person2Name = &quot;person2&quot;;<br />
const string PropertyMapTemplate = &quot;{0} = {1}.{2}&quot;;</p>
<p>var type1 = typeof(Person1);<br />
var type2 = typeof(Person2);</p>
<p>var person1Properties = type1.GetProperties();<br />
var person2Properties = type2.GetProperties();</p>
<p>var mappings = person1Properties.Select(person1Property =&gt;<br />
{<br />
    var person1PropertyName = person1Property.Name;<br />
    var person2PropertyName = person2Properties<br />
        .First(person2Property =&gt; person2Property.Name == person1Property.Name<br />
            &amp;&amp; person2Property.PropertyType == person1Property.PropertyType)<br />
        .Name;</p>
<p>    return string.Format(PropertyMapTemplate, person2PropertyName, person1Name, person1PropertyName);<br />
});<br />
[/sourcecode]</p>
<p>Here we start by defining the name of each variable as it will appear in the generated code, as well as the format string for the mapping statement. We then get the properties of each type and then loop through them using a LINQ expression to get the formatted strings. The result of this when executed is that the mappings variable contains the following two elements</p>
<p>[sourcecode language="csharp"]<br />
[“Name = person1.Name”, “Birthday = person1.Birthday”]<br />
[/sourcecode]</p>
<p>This then needs to be formatted to look like the two lines in our example. We do this with the following line:</p>
<p>[sourcecode language="csharp"]<br />
var mappingDeclarations = string.Join(&quot;,&quot; + Environment.NewLine + &quot;\t\t\t&quot;, mappings);<br />
[/sourcecode]</p>
<p>Writing the template</p>
<p>Now we can finally get to writing the template itself. Because my Person1 and Person2 objects are declared in the current project, I need to add an assembly reference to the current project as well as an import statement to the appropriate namespace, “MapperApplication”.</p>
<p>[sourcecode language="csharp"]<br />
&lt;#@ template debug=&quot;false&quot; hostspecific=&quot;false&quot; language=&quot;C#&quot; #&gt;<br />
&lt;#@ assembly name=&quot;System.Core&quot; #&gt;<br />
&lt;#@ assembly name=&quot;$(TargetPath)&quot; #&gt;<br />
&lt;#@ import namespace=&quot;MapperApplication&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Linq&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Text&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Collections.Generic&quot; #&gt;<br />
&lt;#@ output extension=&quot;.cs&quot; #&gt;<br />
[/sourcecode]</p>
<p>Now let’s go about inserting the logic we previously wrote into this template. We surround all code that we want to be executed with tags.</p>
<p>[sourcecode language="csharp"]<br />
&lt;#@ template debug=&quot;false&quot; hostspecific=&quot;false&quot; language=&quot;C#&quot; #&gt;<br />
&lt;#@ assembly name=&quot;System.Core&quot; #&gt;<br />
&lt;#@ assembly name=&quot;$(TargetPath)&quot; #&gt;<br />
&lt;#@ import namespace=&quot; MapperApplication &quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Linq&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Text&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Collections.Generic&quot; #&gt;<br />
&lt;#@ output extension=&quot;.cs&quot; #&gt;<br />
&lt;#<br />
const string person1Name = &quot;person1&quot;;<br />
const string person2Name = &quot;person2&quot;;<br />
const string PropertyMapTemplate = &quot;{0} = {1}.{2}&quot;;</p>
<p>var type1 = typeof(Person1);<br />
var type2 = typeof(Person2);<br />
var person1Properties = type1.GetProperties();<br />
var person2Properties = type2.GetProperties();</p>
<p>var mappings = person1Properties.Select(person1Property =&gt;<br />
{<br />
    var person1PropertyName = person1Property.Name;<br />
    var person2PropertyName = person2Properties<br />
        .First(person2Property =&gt; person2Property.Name == person1Property.Name<br />
            &amp;&amp; person2Property.PropertyType == person1Property.PropertyType)<br />
        .Name;</p>
<p>    return string.Format(PropertyMapTemplate, person2PropertyName, person1Name, person1PropertyName);<br />
});</p>
<p>var mappingDeclarations = string.Join(&quot;,&quot; + Environment.NewLine + &quot;\t\t\t&quot;, mappings);<br />
#&gt;<br />
[/sourcecode]</p>
<p>Notice that we are starting out by getting all our components needed to build the class together before actually defining the template code itself. This is a good practice to follow as it makes the template code easier to read and understand.</p>
<p>Finally, let’s define the class template. All we need to do is copy our model code from before and substitute in our person1Name and person2Name const values as variable names, as well as the mappingDeclarations string that we generated with our LINQ statement. To print a variable to the template output, we surround it with tags. The final result looks like this:</p>
<p>[sourcecode language="csharp"]<br />
&lt;#@ template debug=&quot;false&quot; hostspecific=&quot;false&quot; language=&quot;C#&quot; #&gt;<br />
&lt;#@ assembly name=&quot;System.Core&quot; #&gt;<br />
&lt;#@ assembly name=&quot;$(TargetPath)&quot; #&gt;<br />
&lt;#@ import namespace=&quot; MapperApplication&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Linq&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Text&quot; #&gt;<br />
&lt;#@ import namespace=&quot;System.Collections.Generic&quot; #&gt;<br />
&lt;#@ output extension=&quot;.cs&quot; #&gt;<br />
&lt;#<br />
const string person1Name = &quot;person1&quot;;<br />
const string person2Name = &quot;person2&quot;;<br />
const string PropertyMapTemplate = &quot;{0} = {1}.{2}&quot;;</p>
<p>var type1 = typeof(Person1);<br />
var type2 = typeof(Person2);<br />
var person1Properties = type1.GetProperties();<br />
var person2Properties = type2.GetProperties();<br />
var mappings = person1Properties.Select(person1Property =&gt;<br />
    {<br />
        var person1PropertyName = person1Property.Name;<br />
        var person2PropertyName = person2Properties<br />
            .First(person2Property =&gt; person2Property.Name == person1Property.Name<br />
                &amp;&amp; person2Property.PropertyType == person1Property.PropertyType)<br />
            .Name;</p>
<p>        return string.Format(PropertyMapTemplate, person2PropertyName, person1Name, person1PropertyName);<br />
    });</p>
<p>var mappingDeclarations = string.Join(&quot;,&quot; + Environment.NewLine + &quot;\t\t\t&quot;, mappings);<br />
#&gt;</p>
<p>namespace MapperApplication<br />
{<br />
    public static class Mapper<br />
    {<br />
        public static &lt;#= type2.Name #&gt; MapTypes(&lt;#= type1.Name #&gt; &lt;#= person1Name #&gt;)<br />
        {<br />
            return new &lt;#= type2.Name #&gt;<br />
            {<br />
                &lt;#= mappingDeclarations #&gt;<br />
            };<br />
        }<br />
    }<br />
}<br />
[/sourcecode]</p>
<p>Now let’s save the template. Saving it will run the T4 file and generate a code file which will appear as a subcomponent of our .tt file in the solution explorer. This is what the generated code looks like:</p>
<p>[sourcecode language="csharp"]<br />
namespace MapperApplication<br />
{<br />
    public static class Mapper<br />
    {<br />
        public static Person2 MapTypes(Person1 person1)<br />
        {<br />
            return new Person2<br />
            {<br />
                Name = person1.Name,<br />
                Birthday = person1.Birthday<br />
            };<br />
        }<br />
    }<br />
}<br />
[/sourcecode]</p>
<p>Success!</p>
<p>This may have seemed like a lot of work to go to just to write a simple mapper, but the advantage of templating becomes clear when we modify the Person1 and Person2 classes. Any modifications are carried over to the mapper when the program is recompiled or the mapper is edited. In large projects the automation of tasks like these can save countless hours of refactoring. Happy templating!</p>
