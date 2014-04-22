---
layout: post
status: publish
published: true
title: Sending Email From C# Via Hotmail/Outlook/Windows Live
author: Levi
author_login: levi_botelho@hotmail.com
author_email: levi_botelho@hotmail.com
excerpt: ! "I recently found myself wanting to send an email from an ASP.NET web application.
  I didn't want to go to all the hassle of setting up an SMTP server just to test
  my code, so decided to do it through an outlook.com email account. Here's how:\r\n<h2>1.
  Set up the account</h2>\r\n<h2>2. Log out and then back into the account</h2>\r\nThis
  tripped me up for a good 20 minutes. Having already written my code I created my
  account and immediately tested it. I kept on running into this seemingly nonsensical
  error:\r\n<blockquote>5.3.4 Requested action not taken; To continue sending messages,
  please sign in to your account.</blockquote>\r\n"
wordpress_id: 32
wordpress_url: http://levibotelho.azurewebsites.net/?p=32
date: !binary |-
  MjAxMy0wNC0xNyAwODowMjowNCArMDIwMA==
date_gmt: !binary |-
  MjAxMy0wNC0xNyAwODowMjowNCArMDIwMA==
categories:
- C#
tags:
- c#
- email
comments:
- id: 20372
  author: Danilo
  author_email: danilolutz@gmail.com
  author_url: ''
  date: !binary |-
    MjAxMy0xMC0xMCAyMDo1ODoyOSArMDIwMA==
  date_gmt: !binary |-
    MjAxMy0xMC0xMCAxODo1ODoyOSArMDIwMA==
  content: Thank you, very helpful
- id: 20452
  author: Rubem
  author_email: rubem@rockes.com.br
  author_url: ''
  date: !binary |-
    MjAxMy0xMC0xOCAxODo0Mjo0MyArMDIwMA==
  date_gmt: !binary |-
    MjAxMy0xMC0xOCAxNjo0Mjo0MyArMDIwMA==
  content: (2) Thank you, very helpful
---
<p>I recently found myself wanting to send an email from an ASP.NET web application. I didn't want to go to all the hassle of setting up an SMTP server just to test my code, so decided to do it through an outlook.com email account. Here's how:</p>
<h2>1. Set up the account</h2>
<h2>2. Log out and then back into the account</h2>
<p>This tripped me up for a good 20 minutes. Having already written my code I created my account and immediately tested it. I kept on running into this seemingly nonsensical error:</p>
<blockquote><p>5.3.4 Requested action not taken; To continue sending messages, please sign in to your account.</p></blockquote>
<p><a id="more"></a><a id="more-32"></a><br />
The reason is simple. Despite the fact that once you create your outlook.com account you are immediately logged in, your account is not verified. To avoid this error, once you create your account you need to log out and log back in, solving a captcha in the process. This will validate your account and allow the following code to work properly. You may be able to skip this step if you validate your account with a telephone number when you create it.</p>
<h2>3. Write the code</h2>
<p>[csharp]<br />
public static class Mailer<br />
{<br />
    const string SmtpServer = &quot;smtp.live.com&quot;;<br />
    const int SmtpPort = 587;<br />
    const string FromAddress = &quot;address@outlook.com&quot;;<br />
    const string Password = &quot;password&quot;;</p>
<p>    public static void SendMail(string toAddress, string subject, string body)<br />
    {<br />
        var client = new SmtpClient(SmtpServer, SmtpPort)<br />
        {<br />
            Credentials = new NetworkCredential(FromAddress, Password),<br />
            EnableSsl = true<br />
        };</p>
<p>        client.Send(FromAddress, toAddress, subject, body);<br />
    }<br />
}<br />
[/csharp]<br />
I've wrapped this in a static class in order to respect the <a title="SRP" href="http://en.wikipedia.org/wiki/Single_responsibility_principle" target="_blank">SRP</a>. Note that the email address and password should in practice be encrypted into a config file instead of being written directly into the source as I have done here for simplicity. The server settings should also be extracted out into a config file so that they can be changed later if need be. I'll leave web.config encryption for another post.</p>
