---
layout: post
title: AngularJS vs KnockoutJS
category: Development
tags: [knockoutjs, angularjs]
description: Choosing between Angular and Knockout can be tough. Here I talk about my experience with the two frameworks and when I would choose to use each one.
comments: true
share: true
redirect_from: "/angularjs-vs-knockoutjs/"
---
Lately I’ve been spending a lot of time with AngularJS. Coming from a KnockoutJS background, one of the first questions I had when I first started out with Angular was how the two frameworks compare. Hopefully this article will provide some answers for those in the same situation that I was.

##Why Knockout is great

While the folks at Knockout state that it is an MVVM framework for JavaScript, I find that the best way to think of it is as a data binding framework. This data binding lets you do two really great things.

+ You can forget about having to make tons of calls all across the DOM to get a hold of field values. Everything is stored in a set of variables in your Knockout view model object which are all updated automatically.
+ Dynamic user interfaces can be easily created. Collections of items can be displayed dynamically onscreen, and elements can be made to pop in and out of view at the flick of a boolean value.

##Why Angular is great

I like to think of Angular as a full-featured front-end web application framework. That is to say that you could easily build an entire application with Angular and *only Angular*. Angular has data binding, but it also has tons of built in features which encapsulate things like Ajax requests and i18n/l10n. It also has a killer API for building single page apps. And to top it all off, it even comes with its own "lite" version of jQuery.

##So what's the difference?

The biggest difference between the two frameworks boils down essentially to the choice between a simple framework that has useful, but limited features, and a complex framework that can do it all.

Angular takes more work to set up than Knockout. Its architecture can seem a bit "elitist" in comparison, requiring more structure than a simple view model object to store values in. And it also takes considerably more time to get comfortable with if you're using the framework for the first time.

Furthermore, if you are trying to integrate Angular into an existing site or are trying to use it alongside other frameworks, you'll usually find points of overlap between Angular and everything else. To give you an idea of what I mean, here are two examples of this that come to mind. (These examples involve ASP.NET, but the same goes for most web frameworks.)

+ If you are using ASP.NET MVC and want to implement a single page app, you'll have to change your thinking about what features are handled by ASP.NET and which are handled by your client-side code. This may seem obvious, but old habits die hard.
+ If you have some form of client-side validation in place in an existing application, you won't be able to take advantage of Angular's data validation features should you choose to integrate it into your app. Don't get me wrong, this isn't a problem, however you may feel like you're not getting as much as you could be out of Angular. You can run into many cases like this, and if you end up having too many you may find that the simplicity/functionality tradeoff that you have to make when adopting Angular just isn't worth it.

##So which should I choose?

I'm not going to go so far as to tell you which framework you should choose for your projects, however I can share with you my own logic for choosing between the two.

If the project is new and I have free-reign over the architecture, then I'll usually go with Angular. It has just too many killer features to pass up, and I love having one framework that lets me do whatever I want to, as opposed to a collection of frameworks that I need to fuse together into one coherent app. Plus, the support for single page apps is just fantastic.

If the project is not new, or if I don't have free reign over the architecture, then I'll tend towards Knockout. Speaking from experience, it isn't too difficult to integrate Knockout into existing projects provided that the code is clean. And because Knockout is simple, you'll get the data binding you want in a simple, easy to use package, which can be just what you need when doing brownfield web development.

The final thing that you'll want to take into consideration is how you want to split your workload between the client and server. Angular tends to take over more server-side work than Knockout, so if you're writing a site with a lightweight framework like NodeJS, it may be a better choice. On the other hand if you're using a more full-featured web framework like ASP.NET, you may prefer to handle more things on the server side, in which case Knockout would probably be the better choice.

In any case, rest assured that both frameworks are fantastic. No matter which one you end up choosing, you'll be happy with it. So take your pick, and get coding! 

