---
layout: post
title: Dead simple Ajax
category: Development
tags: [javascript]
description: All your Ajax needs taken care of in one small library.
comments: true
share: true
---

Ajax boilerplate is a pain to write. If you’re already depending on a large JavaScript framework, chances are you don’t have to think about it. But if you’re not, it’s unfortunate to have to add a reference to jQuery or a similar library just for the Ajax.

So I’ve written a small library to fix this. It’s called dead-simple-ajax, and in just 95 lines of code it manages to do three things really well:

1. Provide promise-enabled Ajax for CommonJS environments (Node JS/Browserify)
2. Deserialize JSON responses (unless you tell it not to)
3. Allow custom request modification via a callback (for adding headers, etc.)

dead-simple-ajax is not right for everybody, but if you want Ajax without the weight of a large do-it-all library, chances are it is right for you.

The API is documented on the [project’s GitHub page](https://github.com/LeviBotelho/dead-simple-ajax) (spoiler, there are only four methods), and you can get it by either installing the npm package (`npm install -S dead-simple-ajax`) or by simply copying the project’s index.js file straight into your project.

As with all my open source projects, I’m always happy to receive feedback. If you use the library and find a bug or have a suggestion, feel free to create an issue on the GitHub page.

Happy Ajaxing.