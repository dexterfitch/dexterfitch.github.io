---
layout: post
title:      "Sinatra: Meal Planner"
date:       2020-06-19 12:34:56 -0400
permalink:  sinatra_meal_planner
---


I was inspired to make this app after probably the 600th time while watching queer ESPN (the Food Network), my boyfriend and I saying to one another "oh we should totally make that" and then promptly forgetting until we're already at the grocery store a week later.

The app was created with the Sinatra framework, and has features such as a unique user login, as well as an associated collection of favourite recipes that users can create, edit, and delete. In order to have this level of functionality, I needed a database, which has been built with ActiveRecord, an ORM (object relational mapping) framework.

The most challenging part of building the app was deciding how to handle images. I considered researching how to setup and upload option, but wanted to focus more on just practicing setting up routes, forms, and the database. As it is I settled on a URL field to let people hotlink (naughty!) an image.

After I felt confident in my understanding of how the app was working, I spent a good portion of time just playing with the user experience and design aspect. I included features for the user like detailed error messages, and I attempted to make a clean and refreshing retro design for the app, inspired by a small collection of cookbooks from the 60's and 70's that my mum had. I hope the site looks more appetizing than the aspics did, at least.