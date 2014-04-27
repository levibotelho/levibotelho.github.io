---
layout: post
title: Sending Email From C# Via Hotmail/Outlook/Windows Live
category: Development
tags: [c#, email]
comments: true
share: true
redirect_from: "/sending-email-from-c-via-hotmailoutlookwindows-live/"
---
I recently found myself wanting to send an email from an ASP.NET web application. I didn't want to go to all the hassle of setting up an SMTP server just to test my code, so decided to do it through an outlook.com email account. Here's how:

## 1. Set up the account

## 2. Log out and then back into the account

This tripped me up for a good 20 minutes. Having already written my code I created my account and immediately tested it. I kept on running into this seemingly nonsensical error:

<blockquote>5.3.4 Requested action not taken; To continue sending messages, please sign in to your account.
</blockquote>
<a id="more"></a><a id="more-32"></a>
The reason is simple. Despite the fact that once you create your outlook.com account you are immediately logged in, your account is not verified. To avoid this error, once you create your account you need to log out and log back in, solving a captcha in the process. This will validate your account and allow the following code to work properly. You may be able to skip this step if you validate your account with a telephone number when you create it.

## 3. Write the code

{% highlight csharp %}
public static class Mailer
{
    const string SmtpServer = "smtp.live.com";
    const int SmtpPort = 587;
    const string FromAddress = "address@outlook.com";
    const string Password = "password";

    public static void SendMail(string toAddress, string subject, string body)
    {
        var client = new SmtpClient(SmtpServer, SmtpPort)
        {
            Credentials = new NetworkCredential(FromAddress, Password),
            EnableSsl = true
        };

        client.Send(FromAddress, toAddress, subject, body);
    }
}
{% endhighlight %}
I've wrapped this in a static class in order to respect the [SRP](http://en.wikipedia.org/wiki/Single_responsibility_principle). Note that the email address and password should in practice be encrypted into a config file instead of being written directly into the source as I have done here for simplicity. The server settings should also be extracted out into a config file so that they can be changed later if need be. I'll leave web.config encryption for another post.

