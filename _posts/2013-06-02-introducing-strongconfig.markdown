---
layout: post
title: Introducing strong.config
category: Open Source
tags: [strong.config, open source]
description: Introducing strong.config: a T4 file that generates a strongly-typed façade for accessing configuration variables.
comments: true
share: true
redirect_from: "/introducing-strongconfi/"
---
I've always been surprised by the obvious lack of strong typing in accessing configuration data. Calls to ConfigurationManager are null reference exceptions waiting to happen, as key changes in the config file or typos in the string passed to the call aren't picked up until they hit you later on with a runtime exception.

I've traditionally managed this problem by writing a façade class that sits between my application code and the configuration file. As this results in having only one weakly-typed reference to each configuration value, the number of errors is greatly reduced. But the façade still needs to be kept up to date...

And that's why I wrote strong.config. strong.config is a single T4 file which automatically generates a strongly-typed façade in front of an application's configuration file. It takes config code like this:

{% highlight xml %}
<!-- The boolean key value. -->
<add key="BooleanKey" value="true"/>
{% endhighlight %}

and turns it into this:

{% highlight csharp %}
/// <summary>
/// The boolean key value.
/// </summary>
public static bool BooleanKey
{
    get { return bool.Parse(ConfigurationManager.AppSettings["BooleanKey"]); }
}
{% endhighlight %}

Feel free to check the project out on Codeplex ([strongconfig.codeplex.com](https://strongconfig.codeplex.com/)) and to use it in your own projects if you think it could help you out. I did spend some time writing the Codeplex documentation so hopefully everything is clear right from the start. However if that's not the case, or if you have a comment or suggestion to make, please leave me a comment either here or on the Codeplex site. All feedback is greatly appreciated!

