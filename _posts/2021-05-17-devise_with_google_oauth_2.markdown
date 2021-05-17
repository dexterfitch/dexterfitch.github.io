---
layout: post
title:      "The Breakdown = Rails5 + Devise + Google OmniAuth Part Deux"
date:       2021-05-17 12:34:56 -0400
permalink:  the_breakdown_rails5_devise_google_oauth_2
---


[Click here](/the_breakdown_rails5_devise_google_oauth) for the first part.

The following video shows you the minimal fields you need to fill out in Google API to get an OAuth Client ID and Client Secret.

<iframe width="1280" height="720" src="https://www.youtube.com/embed/g8FjsMYdErk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Once you have your client ID and client secret, you simply need to edit your .env file to include them, like so:

```
GOOGLE_OAUTH_CLIENT_ID=Your client id
GOOGLE_OAUTH_CLIENT_SECRET=Your client secret
```

And that's it! Whew! Nice work!