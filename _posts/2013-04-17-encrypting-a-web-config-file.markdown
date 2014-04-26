---
layout: post
title: Encrypting a web.config file
category: [Deployment]
tags: [c#, iis, web.config, deployment]
comments: true
share: true
---
## 1. Publish your web application to IIS

## 2. Determine the identity of your application

This is often going to be `IIS APPPOOL[Your application pool name]</strong>. Mine for example is <strong>“IIS APPPOOLDefaultAppPool`.
If you wish to verify this, it is accessible via the following line of code:

{% highlight csharp %}
 System.Security.Principal.WindowsIdentity.GetCurrent().Name
{% endhighlight %}

## 3. Grant ASP.NET read access to the RSA key container

The RSA key container contains the RSA key data that is used to encrypt and decrypt the web.config file. ASP.NET needs to be able to access the key container in order to perform this operation. To grant access, open the command prompt as administrator and navigate to the following directory:

**C:WindowsMicrosoft.NETFramework[.NET Framework Version]**

Then, execute the following command:

{% highlight powershell %}
 aspnet_regiis –pa “NetFrameworkConfigurationKey” “[Your application identity]”
{% endhighlight %}

Don’t forget the quotation marks around the final two values. If this fails, you may have forgotten to run the command prompt as administrator. Keep your command prompt open.
<a id="more"></a><a id="more-52"></a>

## 4. Generate the keys

The keys for encryption and decryption need to be generated and written to your web.config file. This is done after the application is published to IIS. In IIS8 generating keys is easy. Simply navigate to your website in the IIS manager and click on “Machine Key”.

[](http://thepragmaticdeveloper.files.wordpress.com/2013/01/wc1.png)

Then in the following window in the “Actions” bar on the right, click “Generate Keys”, followed by “Apply”.

[](http://thepragmaticdeveloper.files.wordpress.com/2013/01/wc2.png)

Now, open your deployed web.config file and verify that there is an entry called

{% highlight xml %}

{% endhighlight %}

What this does is insert a key into the web.config file of the selected application, allowing its sections to be encrypted and decrypted by IIS.

## 5. Encrypt the data

Go back to the command prompt that you used to grant access to the RSA container. Type the following command:

{% highlight powershell %}
aspnet_regiis -pe “[Path to the config element]” -app “/[Virtual path to your application]”
{% endhighlight %}

Again, don’t forget the quotation marks. <span style="line-height:1.6;">This command does two main things :</span>

<ol>
<li>Looks in the configuration file of the given application for the given configuration element and encrypts it.</li>
<li>Specifies to ASP.NET that this element in this application needs to be decrypted before it is accessed.</li>
</ol>
Once you have finished encrypting the desired sections, you need to finish by encrypting the machine key. Do this by passing `system.web/machineKey` as the first argument to the above command.

That’s all there is to it. IIS will be able to read the encrypted configuration settings and your sensitive data will be obscured from anybody who has access to the web.config. Should you ever need to decrypt a section, simply run the encryption command, specifying **-pd** instead of **-pe**.

