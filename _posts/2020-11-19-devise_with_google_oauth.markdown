---
layout: post
title:      "The Breakdown = Rails5 + Devise + Google OmniAuth"
date:       2020-11-19 12:34:56 -0400
permalink:  the_breakdown_rails5_devise_google_oauth
---


> Greetings, weary traveller. I am no sage. I have no wisdom to depart. I barely even know what I'm doing. But I **DO** have this: what I hope is a pretty good guide to quickly getting a secure and user friendly account creation and login system working in your rails app during development by leveraging [Devise](https://github.com/heartcombo/devise) and [OmniAuth](https://github.com/omniauth/omniauth) with [Google API](https://console.cloud.google.com/). There's a lot of guides out there on this particular process, and for a reason: it's gosh-darn fiddly. I figure having one more demonstration and a fresh approach can't hurt. Good luck and safe journey!


## The Basics

#### Step Zero: Have a rails app ready for User model generation. 

> If you're already lost, I am sorry. [Go in peace, friend](https://www.youtube.com/watch?v=5qap5aO4i9A).

#### [Step One](#step_one): Get some gems and bundle those suckers.

#### [Step Two](#step_two): Generate your User model with Devise.

#### [Step Three](#step_three): Configure your app for OmniAuth. 

#### [Step Four](#step_four): Create and configure your project in Google to get API keys.

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
These commands will install Devise to your rails app, and set up the User model, and the associated migration for the user, as well as the routes and views for signing up and signing in, changing a password, etc.

***

<h3 style="margin-bottom: 0"><span id="step_three">Step Three</span></h3>
<h4 style="margin-top: 4px">Prepare your app for OmniAuth.</h4>

First, let's open our main application layout file (`*/app/views/layouts/application.html.erb`) and create a space for Devise's error messages to appear.

Right above the `<%= yield %>`, add:
```
<p class="notice"><%= notice %></p>
<p class="alert"><%= alert %></p>
```

Next, let's have a look at our user model (`*/app/models/user.rb`). Thanks to Devise's generation method, the file should look like this:
```
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable
end
```

Let's expand the user model for OmniAuth support, by adding the OmniAuth Devise modules, as well as a method for collecting and appending the data coming from Users' Google profiles when they connect with their Google account. Update your user model file to look like this:
```
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable,
         :omniauthable, omniauth_providers: [:google_oauth2]
  
  def self.from_google(uid:, email:, full_name:, avatar_url:)
    user= User.find_or_create_by(email: email) do |u|
      u.uid = uid
      u.full_name = full_name
      u.avatar_url = avatar_url
      u.password = SecureRandom.hex
    end
    user.update(uid: uid, full_name: full_name, avatar_url: avatar_url)
    # BLOG
    user
  end
end
```

Although Devise has neatly handled all the controller methods in the background for registering, signing in, and signing out users natively in our app, we still need a special controller for handling the same processes via Google's API through OmniAuth callbacks. Create a folder called `users` in your project's controllers folder (`*/app/controllers`).

In this folder (`*/app/controllers/users/`), create a file called `omniauth_callbacks_controller.rb` and paste the following into the file (`*/app/controllers/users/omniauth_callbacks_controller.rb`):

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def google_oauth2
    user = User.from_google(from_google_params)
    
    if user.present?
      sign_out_all_scopes
      flash[:success] = t 'devise.omniauth_callbacks.success', kind: 'Google'
      # BLOG
      sign_in_and_redirect user, event: :authentication
    else
      flash[:alert] = t 'devise.omniauth_callbacks.failure', kind: 'Google', reason: "#{auth.info.email} is not authorized."
      redirect_to new_user_session_path
    end
  end

  protected

  def after_omniauth_failure_path_for(_scope)
    new_user_session_path
  end

  def after_sign_in_path_for(resource_or_scope)
    stored_location_for(resource_or_scope) || root_path
  end

  private

  def from_google_params
    @from_google_params ||= {
      uid: auth.uid,
      email: auth.info.email,
      full_name: auth.info.name,
      avatar_url: auth.info.image
    }
  end

  def auth
    @auth ||= request.env['omniauth.auth']
  end
end
```

> Holy shit, right? I told you this was fiddly. Hang in there, we're about halfway through configuration now.

Open up your `*/config/routes.rb` file. It should look like this presently:
```
Rails.application.routes.draw do
  devise_for :users
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end
```

We need to expand the devise user routes to include the OmniAuth callbacks, and we also need to define a root path. Change your file so it looks something like this. You can set your root view to whatever you like, but for this example, I'm presuming that the homepage is a static page. We also need the corresponding `static_controller.rb` we'll create to define privacy and terms of service pages that the Google API will request later during our setup.
Change `*/config/routes.rb` to:
```
Rails.application.routes.draw do
  devise_for :users, controllers: { omniauth_callbacks: 'users/omniauth_callbacks' }
  root to: 'static#homepage'
  get '/privacy', as: 'privacy', to: 'static#privacy'
  get '/tos', as: 'tos', to: 'static#tos'
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end
```

Now in your `*/app/controllers/` folder, create `static_controller.rb`, and within `*/app/controllers/static_controller.rb` add:
```
class StaticController < ApplicationController

  def homepage
  end

  def privacy
  end

  def tos
  end

end
```

And also create a `static` views folder and corresponding view files for each method: `*/app/views/static/homepage.html.erb`, `*/app/views/static/privacy.html.erb`, `*/app/views/static/tos.html.erb`

> OK, deep breath. Almost there.

When we ran our Devise generators, we created a migration for our User model. Open `*/db/migrate/##############_devise_create_users.rb` - it should look like this:
```
# frozen_string_literal: true

class DeviseCreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      ## Database authenticatable
      t.string :email,              null: false, default: ""
      t.string :encrypted_password, null: false, default: ""

      ## Recoverable
      t.string   :reset_password_token
      t.datetime :reset_password_sent_at

      ## Rememberable
      t.datetime :remember_created_at

      ## Trackable
      # t.integer  :sign_in_count, default: 0, null: false
      # t.datetime :current_sign_in_at
      # t.datetime :last_sign_in_at
      # t.string   :current_sign_in_ip
      # t.string   :last_sign_in_ip

      ## Confirmable
      # t.string   :confirmation_token
      # t.datetime :confirmed_at
      # t.datetime :confirmation_sent_at
      # t.string   :unconfirmed_email # Only if using reconfirmable

      ## Lockable
      # t.integer  :failed_attempts, default: 0, null: false # Only if lock strategy is :failed_attempts
      # t.string   :unlock_token # Only if unlock strategy is :email or :both
      # t.datetime :locked_at


      t.timestamps null: false
    end

    add_index :users, :email,                unique: true
    add_index :users, :reset_password_token, unique: true
    # add_index :users, :confirmation_token,   unique: true
    # add_index :users, :unlock_token,         unique: true
  end
end
```

We need to update the User table to include columns for data from Google. Update your file to look like this:
```
# frozen_string_literal: true

class DeviseCreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      ## Database authenticatable
      t.string :email,              null: false, default: ""
      t.string :encrypted_password, null: false, default: ""

      ## Google OAuth Columns
      t.string :full_name
      t.string :uid
      t.string :avatar_url

      ## Recoverable
      t.string   :reset_password_token
      t.datetime :reset_password_sent_at

      ## Rememberable
      t.datetime :remember_created_at

      ## Trackable
      # t.integer  :sign_in_count, default: 0, null: false
      # t.datetime :current_sign_in_at
      # t.datetime :last_sign_in_at
      # t.inet     :current_sign_in_ip
      # t.inet     :last_sign_in_ip

      ## Confirmable
      # t.string   :confirmation_token
      # t.datetime :confirmed_at
      # t.datetime :confirmation_sent_at
      # t.string   :unconfirmed_email # Only if using reconfirmable

      ## Lockable
      # t.integer  :failed_attempts, default: 0, null: false # Only if lock strategy is :failed_attempts
      # t.string   :unlock_token # Only if unlock strategy is :email or :both
      # t.datetime :locked_at


      t.timestamps null: false
    end

    add_index :users, :email,                unique: true
    add_index :users, :reset_password_token, unique: true
    # add_index :users, :confirmation_token,   unique: true
    # add_index :users, :unlock_token,         unique: true
  end
end
```

> GOTCHA moment: Notice in the above, I have designated the column type for the Google UID (Google's own user ID) as a `string` and not an `integer`. Why is that? Well, it turns out that the UID is so long (over 10 characters!), that if you set it as an `integer`, you can run into problems depending on which database you're using. Postgres will complain if you set it as any type but `string`, and this column classification works fine for all other Rails databases.

Next, create a nameless .env file in the top level of your project(`*/`), then open up your `*/.gitignore` file and add this to the bottom:

```
# Ignore Google OmniAuth API Key and Secret for security
.env
```

Open `*/config/initializers/devise.rb` and scroll down towards the bottom of the file until you see the `===> Omniauth` section, and add this line, uncommented, below:
`config.omniauth :google_oauth2, ENV['GOOGLE_OAUTH_CLIENT_ID'], ENV['GOOGLE_OAUTH_CLIENT_SECRET']`

> WHEW. Go get a glass of water. Take a stroll around the block. Stare at something alive and green for an indeterminate period of time. Then meet me back here because we only have one more hairy part to tackle.

***

<h3 style="margin-bottom: 0"><span id="step_four">Step Four</span></h3>
<h4 style="margin-top: 4px">Create and configure your project in Google to get API keys.</h4>

Next, you have to go to [Google's API](https://console.cloud.google.com/projectcreate) and create your project there.

[Click here](/the_breakdown_rails5_devise_google_oauth_2) for the next part.