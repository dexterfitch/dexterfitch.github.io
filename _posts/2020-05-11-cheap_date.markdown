---
layout: post
title:      "CLI Project: Cheap Date"
date:       2020-05-11 12:34:56 -0400
permalink:  cheap_date
---


This project was conceived while talking about how annoying it can be sometimes trying to narrow down a place to eat. And I'm also a cheapskate, so Cheap Date was born.

The APIs referenced are Zomato and Geocode.xyz. I ended up needing two APIs in order for User Experience to be smooth and painless. Zomato is a great free API that gives you restaurant data, but the locations are given as latitude and longitude. It would be really inconvenient for a user to have to know their lat/long in order to see nearby restaurants, so Geocode.xyz came to the rescue, allowing Users to input their ZipCode, converting in to lat/long, and inputting that into Zomato searches.

The other data was relatively straightforward to parse, and it was fun running the experiments with different addresses I've had over the years and seeing old favourite restaurants appear reliably. 

Perhaps the most fun discovery during this project, was that emojis work in the command line. I really liked that little decorative addition and that it also helped me separate data into sections that were immediately visually apparent and organized, something that can be challenging in a CLI.