---
layout: post
title:      "The Breakdown = Rails 5 + Devise + Google OmniAuth"
date:       2020-11-19 12:34:56 -0400
permalink:  the_breakdown_rails5_devise_google_oauth
---


> Greetings, weary traveller. I am no sage. I have no wisdom to depart. I barely even know what I'm doing. But I **DO** have this: what I hope is a pretty good guide to quickly getting a secure and user friendly account creation and login system working in your rails app during development by leveraging [Devise](link) and [OmniAuth](link) with [Google API](link). There's a lot of good guides out there on this particular process, and for a reason: it's gosh-darn fiddly. I figure having one more demonstration and a fresh approach can't hurt. Good luck and safe journey!


## The Basics

#### Step Zero: Have a rails app ready for User model generation. 

> If you're already lost, I am sorry. [Go in peace, friend](link).

#### [Step One](#step_one): Get some gems and bundle those suckers.

#### [Step Two](#step_two): Generate your User model with Devise.

#### [Step Three](#step_three): Create and configure your project in Google to get API keys.

#### [Step Four](#step_four): Configure your app for OmniAuth. 

#### [Step Five](#step_five): Copy and paste [this](link) callbacks controller

***

<h3 style="margin-bottom: 0"><span id="step_one">Step One</span></h3>
<h4 style="margin-top: 4px">Get some gems and bundle those suckers.</h4>

The gems you'll need for this are mostly intuitive:

```
gem 'devise'
gem 'omniauth'
gem 'omniauth-google-oauth2'
gem 'dotenv-rails'
```
Copy and paste this block into your Gemfile, and then run `bundle`. The only gem that might look out of place here is `dotenv` - this gem is necessary for storing our API keys from Google in a `.env` file.

***

<h3 style="margin-bottom: 0"><span id="step_two">Step Two</span></h3>
<h4 style="margin-top: 4px">Generate your User model with Devise.</h4>

Devise's generate methods handle a lot of the heavy lifting for us. In your terminal:

```
rails generate devise:install
rails generate devise User
```
These commands will install Devise to your rails app, and set up the User model, associated migration, and the views for signing up, in, and out.

***

<h3 style="margin-bottom: 0"><span id="step_three">Step Three</span></h3>
<h4 style="margin-top: 4px">Create and configure your project in Google to get API keys.</h4>

***

<h3 style="margin-bottom: 0"><span id="step_four">Step Four</span></h3>
<h4 style="margin-top: 4px">Configure your app for OmniAuth.</h4>
(.env, gitignore, devise.rb, user model?)

Gotchas: UID as string in User Database if using PostGres

development.rb:
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

application.html.erb
<p class="notice"><%= notice %></p>
<p class="alert"><%= alert %></p>

routes.rb
define root_path

***

<h3 style="margin-bottom: 0"><span id="step_five">Step Five</span></h3>
<h4 style="margin-top: 4px">Copy and paste this callbacks controller</h4>