---
layout: post
title:      "Promises, Promises"
date:       2021-02-26 12:34:56 -0400
permalink:  javascript_promises
---

#### What is a JavaScript Promise object, and why is it essential for asynchronous operations?

Alas, there is no cute pinky-hook or an exchange of rings with these promises. The [official docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) define JS promises as objects "that allow you to defer further actions until after a previous action has completed, or respond to its failure." That's great, but what does that mean?

Let's get metaphorical. Consider what happens when you go out shopping for groceries. 

When our scripts are run, they're minified into one long single file line, and typically run all at once from the beginning to the end of the file. This is great for performance, and queues remain an orderly and efficient process everywhere from in our scripts to the store checkout. But there is, occasionally, an major drawback to this system. 

What happens when one of our functions, let's call it the Karen function, makes a request to an API for example, or in other words, wants to see the manager? It seems like Karen and their kids (read: callback functions) are going to take a long time, and there's still a long line of functions waiting their turn, who aren't dependent on Karen. Should the rest of our functions just stand idle, and leave our hungry users at home waiting for their groceries - er, web pages? Nah, instead we can promise Karen that a manager is on the way, and have Karen step aside until the manager, or the server, can respond. While Karen and their kids are waiting, the other functions that aren't dependent on Karen can proceed, without holding up the line. Eventually, a manager will arrive and give some kind of response to Karen, that either allows the Karen function and their callback function(s) to complete, or alternatively, at least finds some graceful way to give Karen a negative response.

I'm sorry Karen functions of the world. Your name really doesn't deserve to be maligned. Usually.