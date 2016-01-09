---
layout: post
title: Introduction to Design Time Templating in C#
category: Development
tags: [c#, T4]
description: An introduction to using T4 templates to pre-generate C# code.
comments: true
share: true
redirect_from: "/introduction-to-design-time-templating-in-c/"
---
While a good developer will always do their best to write DRY code, cases do arise when repetition is inevitable. Mapping one object type to another provides a good example of this. While reflection can be used to compare types against one another, in high-volume contexts the performance loss that reflection incurs may be too significant.

So, if we can’t use reflection to map one object to another, we need another way to accomplish this. We could write a direct mapper method, mapping one type to another manually, but this is a lot of work and requires us to update the mapper manually each time the classes change. This is where T4 templates come in.
<a id="more"></a><a id="more-92"></a>
T4 templates are files which when run generate some sort of textual output, usually code. They look a lot like .aspx files, and function in pretty much the exact same way.

Two types of T4 templates exist: runtime and design time. Runtime templates are executed while the program is running, exactly like .aspx files do, inserting dynamic data into a static skeleton. Design time templates do the same thing, but instead execute pre-compilation. This is an incredibly useful feature, because it allows us to generate C# code that will be error-checked by the compiler just as hand-written code would be. It is this solution that we are going to adopt to map our objects.

For this example we will use two objects, Person1 and Person2 which both look like the following:

{% highlight csharp %}
public class Person1 // (Person2)
{
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
}
{% endhighlight %}

Let’s get templating.

# Creating the template

When you create a T4 template in Visual Studio (they are found in the “General” section of the “Add New Item” window), the file by default contains the following six lines:

{% highlight csharp %}
<#@ template debug="false" hostspecific="false" language="C#" #>
<#@ assembly name="System.Core" #>
<#@ import namespace="System.Linq" #>
<#@ import namespace="System.Text" #>
<#@ import namespace="System.Collections.Generic" #>
<#@ output extension=".txt" #>
{% endhighlight %}

The “template” declaration specifies whether debugging is enabled, the language in which the template is written, and whether the template is “host specific”. A host specific template allows access to a property called “Host” which enables several actions such as the resolution of filepaths. [A full list of these actions can be found here](http://msdn.microsoft.com/en-us/library/microsoft.visualstudio.texttemplating.itexttemplatingenginehost.aspx).

The “assembly” declaration points to assemblies which are loaded into the template. This is similar to adding a reference in a project.

The “import” statement functions just as a “using” statement does, importing namespaces into the template file. Because Intellisense is currently not supported by default in T4 templates, forgetting to import a namespace is a common error to make when writing a template.

Finally, the output statement specifies what format the outputted file should take. In our case we want this to be “.cs”.

# Writing the template logic

Let’s start writing. We’ll start by writing a direct mapper in plain old C#, from which we can derive our template.

{% highlight csharp %}
namespace MapperApplication
{
    public static class Mapper
    {
        public static Person2 Map(Person1 person1)
        {
            return new Person2
            {
                Name = person1.Name,
                Birthday = person1.Birthday
            };
        }
    }
}
{% endhighlight %}

Now let’s start the template. All we are really trying to achieve for the sake of this example is to replace the two innermost lines of code with calculated output from our template. The rest will remain the same. Let’s start by writing the C# to produce these lines of code given the two types:

{% highlight csharp %}
const string person1Name = "person1";
const string person2Name = "person2";
const string PropertyMapTemplate = "{0} = {1}.{2}";

var type1 = typeof(Person1);
var type2 = typeof(Person2);

var person1Properties = type1.GetProperties();
var person2Properties = type2.GetProperties();

var mappings = person1Properties.Select(person1Property =>
{
    var person1PropertyName = person1Property.Name;
    var person2PropertyName = person2Properties
        .First(person2Property => person2Property.Name == person1Property.Name
            &amp;&amp; person2Property.PropertyType == person1Property.PropertyType)
        .Name;

    return string.Format(PropertyMapTemplate, person2PropertyName, person1Name, person1PropertyName);
});
{% endhighlight %}

Here we start by defining the name of each variable as it will appear in the generated code, as well as the format string for the mapping statement. We then get the properties of each type and then loop through them using a LINQ expression to get the formatted strings. The result of this when executed is that the mappings variable contains the following two elements

{% highlight csharp %}
[“Name = person1.Name”, “Birthday = person1.Birthday”]
{% endhighlight %}

This then needs to be formatted to look like the two lines in our example. We do this with the following line:

{% highlight csharp %}
var mappingDeclarations = string.Join("," + Environment.NewLine + "\t\t\t", mappings);
{% endhighlight %}

Writing the template

Now we can finally get to writing the template itself. Because my Person1 and Person2 objects are declared in the current project, I need to add an assembly reference to the current project as well as an import statement to the appropriate namespace, “MapperApplication”.

{% highlight csharp %}
<#@ template debug="false" hostspecific="false" language="C#" #>
<#@ assembly name="System.Core" #>
<#@ assembly name="$(TargetPath)" #>
<#@ import namespace="MapperApplication" #>
<#@ import namespace="System.Linq" #>
<#@ import namespace="System.Text" #>
<#@ import namespace="System.Collections.Generic" #>
<#@ output extension=".cs" #>
{% endhighlight %}

Now let’s go about inserting the logic we previously wrote into this template. We surround all code that we want to be executed with tags.

{% highlight csharp %}
<#@ template debug="false" hostspecific="false" language="C#" #>
<#@ assembly name="System.Core" #>
<#@ assembly name="$(TargetPath)" #>
<#@ import namespace=" MapperApplication " #>
<#@ import namespace="System.Linq" #>
<#@ import namespace="System.Text" #>
<#@ import namespace="System.Collections.Generic" #>
<#@ output extension=".cs" #>
<#
const string person1Name = "person1";
const string person2Name = "person2";
const string PropertyMapTemplate = "{0} = {1}.{2}";

var type1 = typeof(Person1);
var type2 = typeof(Person2);
var person1Properties = type1.GetProperties();
var person2Properties = type2.GetProperties();

var mappings = person1Properties.Select(person1Property =>
{
    var person1PropertyName = person1Property.Name;
    var person2PropertyName = person2Properties
        .First(person2Property => person2Property.Name == person1Property.Name
            &amp;&amp; person2Property.PropertyType == person1Property.PropertyType)
        .Name;

    return string.Format(PropertyMapTemplate, person2PropertyName, person1Name, person1PropertyName);
});

var mappingDeclarations = string.Join("," + Environment.NewLine + "\t\t\t", mappings);
#>
{% endhighlight %}

Notice that we are starting out by getting all our components needed to build the class together before actually defining the template code itself. This is a good practice to follow as it makes the template code easier to read and understand.

Finally, let’s define the class template. All we need to do is copy our model code from before and substitute in our person1Name and person2Name const values as variable names, as well as the mappingDeclarations string that we generated with our LINQ statement. To print a variable to the template output, we surround it with tags. The final result looks like this:

{% highlight csharp %}
<#@ template debug="false" hostspecific="false" language="C#" #>
<#@ assembly name="System.Core" #>
<#@ assembly name="$(TargetPath)" #>
<#@ import namespace=" MapperApplication" #>
<#@ import namespace="System.Linq" #>
<#@ import namespace="System.Text" #>
<#@ import namespace="System.Collections.Generic" #>
<#@ output extension=".cs" #>
<#
const string person1Name = "person1";
const string person2Name = "person2";
const string PropertyMapTemplate = "{0} = {1}.{2}";

var type1 = typeof(Person1);
var type2 = typeof(Person2);
var person1Properties = type1.GetProperties();
var person2Properties = type2.GetProperties();
var mappings = person1Properties.Select(person1Property =>
    {
        var person1PropertyName = person1Property.Name;
        var person2PropertyName = person2Properties
            .First(person2Property => person2Property.Name == person1Property.Name
                &amp;&amp; person2Property.PropertyType == person1Property.PropertyType)
            .Name;

        return string.Format(PropertyMapTemplate, person2PropertyName, person1Name, person1PropertyName);
    });

var mappingDeclarations = string.Join("," + Environment.NewLine + "\t\t\t", mappings);
#>

namespace MapperApplication
{
    public static class Mapper
    {
        public static <#= type2.Name #> MapTypes(<#= type1.Name #> <#= person1Name #>)
        {
            return new <#= type2.Name #>
            {
                <#= mappingDeclarations #>
            };
        }
    }
}
{% endhighlight %}

Now let’s save the template. Saving it will run the T4 file and generate a code file which will appear as a subcomponent of our .tt file in the solution explorer. This is what the generated code looks like:

{% highlight csharp %}
namespace MapperApplication
{
    public static class Mapper
    {
        public static Person2 MapTypes(Person1 person1)
        {
            return new Person2
            {
                Name = person1.Name,
                Birthday = person1.Birthday
            };
        }
    }
}
{% endhighlight %}

Success!

This may have seemed like a lot of work to go to just to write a simple mapper, but the advantage of templating becomes clear when we modify the Person1 and Person2 classes. Any modifications are carried over to the mapper when the program is recompiled or the mapper is edited. In large projects the automation of tasks like these can save countless hours of refactoring. Happy templating!

