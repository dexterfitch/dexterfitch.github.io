---
layout: post
title:      "React, Redux, and Crying into your TV Dinner"
date:       2021-05-14 12:34:56 -0400
permalink:  react_redux_tvdinner
---

#### Understanding Fetch in React Redux

I recently MVP-completed my first React/Redux app, and had a LOT of confusion when it came to following the data as it travelled around my app. I thought I'd break down what's happening in that lifecycle, when we go to fetch data from a database and then render that data in a view.

To start with, let's define the story: A user accesses a web page that will display data from the database. There's a lot of steps inbetween to go from 'User accesses webpage' to 'Webpage displays database data.'

1. The component is accessed
2. Before rendering the component, a fetch action is dispatched
3. The fetch action triggers a fetch actionCreator
4. The fetch actionCreator returns a promise
5. The component continues loading asyncronously but does not render the data yet
6. The promise then becomes a response from the database
7. The response is JSON-ified and dispatched to the reducer
8. The reducer takes in the response, and modifies the store accordingly
9. The data in the store is then processed and rendered by the component.
