---
layout: post
title:      "devise-jwt Authentication with a Rails API"
date:       2020-02-19 17:39:06 +0000
permalink:  devise-jwt_authentication_with_a_rails_api
---

I just finished up work on a project that required a Ruby on Rails driven API as a backend, and I had a devil of a time navigating how to  make password authentication work with it.

If you're looking to do the same, look no further! The answers to your questions (hopefully) lie below.

## First Things First
To begin with, we'll need our Rails API backend. The easiest way to do this is start with this:
```
$ rails new yourAPIname --api
```
This allows us to have only what we need for an API. No views, no nonsense.

Next, you'll need to go to your newly formed Gemfile, and change a few things here. First, there is a line already in the gemfile that we'll need to uncomment:
```
# Use Rack CORS for handling Cross-Origin Resource Sharing (CORS), making cross-origin AJAX possible
# gem 'rack-cors'
```

If we uncomment this gem, it will allow Rails to accept requests from our front end. Normally, Rails only accepts requests from the same origin, but since we only have an API backend through rails, that won't be possible. Therefore, we need this gem to enable cross-origin resource sharing.

Of course, we also need to add the devise-jwt to our Gemfile,  and the dotenv-rails gem will come in handy as well:
```
gem 'devise-jwt'
gem 'dotenv-rails'
```

This gem has devise as a dependency, so when we bundle our gems, it will all get installed. Which is the next step! Navigate to your Rails directory in your terminal, and enter the following:
```
$ bundle install
```

After our gems are bundled, we need to install devise for our Rails app, like so:
```
$ rails g devise:install
```
This will likely prompt something similar to the following instructions in your terminal:
```
===============================================================================

Some setup you must do manually if you haven't yet:

  1. Ensure you have defined default url options in your environments files. Here
     is an example of default_url_options appropriate for a development environment
     in config/environments/development.rb:

       config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

     In production, :host should be set to the actual host of your application.

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       root to: "home#index"

  3. Ensure you have flash messages in app/views/layouts/application.html.erb.
     For example:

       <p class="notice"><%= notice %></p>
       <p class="alert"><%= alert %></p>

  4. You can copy Devise views (for customization) to your app by running:

       rails g devise:views

===============================================================================
```

You really only have to worry about the first point. Simply copying and pasting their example anywhere inside your config/environments/development.rb file will do the trick, unless you have a more particular set up that you're looking for.

The rest of the instructions are not applicable since we're using Rails for an API.

## Getting Underway
We've done the initial set up, now it's time to start getting to the nitty gritty. 

From here on out, I'm going to assume that the name of the model that you're using for user authentication is 'User'. If not, simply subsititute your model's name wherever you see User.

The next step is to generate your model with devise:
```
$ rails g devise User
```

We'll come back to the user model, but the next step is to create our model for how we're managing our tokens--I found that the simplest way was to use the recommended method for the blacklist. In order to do that, we're going to need to make another model:
```
$ rails g model jwt_blacklist jti:string:index exp:datetime
```
The JwtBlacklist will be used by devise-jwt to keep track of which tokens are no longer good. I won't be going too in depth here about how devise-jwt works behind the scenes. If you want to know more, take a look at the docs [here](https://github.com/waiting-for-dev/devise-jwt) and [here](https://github.com/waiting-for-dev/devise-jwt/wiki/Configuring-devise-for-APIs). 

Let's go to our JwtBlacklist model in the app/models directory. Let's add an include statement for the devise-jwt blacklist strategy module. This is how it should look in jwt_blacklist.rb:
```
class JwtBlacklist < ApplicationRecord
  include Devise::JWT::RevocationStrategies::Blacklist

  self.table_name = 'jwt_blacklist'
end

```

Now let's take a look at the migration we generated for this model in the db/migrate directory. The filename should be a long string of numbers, followed by "create_jwt_blacklists.rb"  Let's remove the 's' at the end of the filename, the class name, and the table name, and add "null: false" to the end of each field in the table.

If you don't want to worry about removing the pluralization, you can generate the migration separately from the model, though I find this method easier.

It should end up looking like this:
```
class CreateJwtBlacklist < ActiveRecord::Migration[6.0]
  def change
    create_table :jwt_blacklist do |t|
      t.string :jti, null: false
      t.datetime :exp, null: false

      t.timestamps
    end
    add_index :jwt_blacklist, :jti
  end
end

```

Remember that I said we'd go back to the User model? Now's the time. Let's add some devise-jwt spice to the model. It should look like this:
```
class User < ApplicationRecord
  devise :database_authenticatable,
         :jwt_authenticatable, jwt_revocation_strategy: JwtBlacklist
end
```

You can keep the other devise functionality in your model if you like--like ":registerable, :recoverable, :rememberable," etc.--though I haven't tested how much of it still works with devise-jwt. But it doesn't interfere with devise-jwt at the least.

Now we can migrate our tables to the database:
```
$ rails db:migrate
```

## We've Come to the Finnicky Bits
You might be thinking around this time that we must have added that dotenv-rails gem for a reason. And you'd be right! Let's create a file called .env in the main directory.

We're going to take advantage of this gem by creating an evironment variable that lives in our code base but won't get pushed to public repos. Devise-jwt needs a secret variable to create tokens with, and this secret should be different than the one that devise uses to salt the passwords to be more secure.

Creating a secret is easy. Run the following:
```
$ rails secret
```
It should generate a long sequence of numbers and letters. Copy that over to the .env file with 'DEVISE_JWT_SECRET_KEY=' in front, so that it looks something like this:
```
DEVISE_JWT_SECRET_KEY=a1cce9c68e572ebb6614ab058092570becbdaa0451788eae9445164cc56dc142a92c5eccabe2a7d01784ae758a3392410af839c7f116ab05199961a9820f3840
```

Don't just copy and paste the one I've used above! It's public, so it won't be secure.

Now, head over to your config/initializers/devise.rb file. Add the following inside the Devise setup do |config| :
```
config.jwt do |jwt|
  jwt.secret = ENV['DEVISE_JWT_SECRET_KEY']
  jwt.dispatch_requests = [
    ['POST', %r{^/login$}]
  ]
  jwt.revocation_requests = [
    ['DELETE', %r{^/logout$}]
  ]
end
```

There's a variety of other settings you can explore in the docs, but this will get us up and moving. You can also find the config option for navigational formats and set that to an empty array, since we're just using an API, like so:
```
config.navigational_formats = []
```

Unfortunately, it seems we have to override the default Devise controllers, but it isn't too bad. We should create our own sessions_contoller.rb in app/controllers that takes the following shape:
```
class SessionsController < Devise::SessionsController
  respond_to :json

  private

  def respond_with(resource, _opts = {})
    render json: resource
  end

  def respond_to_on_destroy
    head :no_content
  end
end
```

The application_controller.rb provided in app/controllers will need to be modified as well, just by adding "respond_to :json":

```
class ApplicationController < ActionController::API
  respond_to :json
end
```

Now, the config/routes.rb file will have to be modified as well. I had a variety of problems with authentication until I added the defaults: { format: :json } to the end. It should look like this:
```
Rails.application.routes.draw do
  devise_for :users, controllers: { sessions: 'sessions' }, defaults: { format: :json }
end
```

## Almost There
We can't forget about setting up cors! We enabled the rack-cors gem at the start, and we have to configure that as well. Put the following in your config/application.rb file:
```
config.middleware.insert_before 0, Rack::Cors do
      allow do
        origins '*'
        resource '*',
          headers: :any, 
          expose: ['Authorization'],
          methods: :any
      end
    end
```

Now, this setup is only appropriate for a development and testing environment, not production. The origins that will be allowed access should be specified, otherwise it is not secure.

To make sure you're actually using the authentication, in all your controllers that are accessing material that only logged in users should have access to, put the following at the top of the controller:
```
before_action authenticate_user!
```

Now all the set up is done! You're ready to start using token authentication!

## But how?
It's all in the headers. When your user authenticates their identity by providing their password, Rails (with devise-jwt) will send back a long string that looks something like this:
```
Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwic2NwIjoidXNlciIsImF1ZCI6bnVsbCwiaWF0IjoxNTgyMTMxNTY0LCJleHAiOjE1ODIyMTc5NjQsImp0aSI6IjYwMDJiMWMyLTg0MDUtNGQ4YS1hYWZmLTZjMjdmNmFiYWRkZiJ9.hzASVznJTflhkDVcGdptLZxjRG3b6m_sfj3NcOvJIUs
```

You will then need to pass this token back with your next request to prove that you should still have access. This is all done through the 'Authorization' header in the http requests and responses that are sent. You will need to keep track of which token is current for a user.

I found the following headers to work for my http requests:

For 'GET', 'POST' and 'PATCH' requests:
```
{
  "Content-Type": "application/json",
  "Accept": "application/json",
  "Authorization": currentToken
}
```

For 'POST' requests for signing in or signing up a user (since they won't have a token yet):

```
{
    "Content-Type": "application/json",
    "Accept": "application/json"
  }
```

For 'DELETE' requests, since they won't be sending in any JSON:
```
{
  "Accept": "application/json",
	"Authorization": currentToken
}
```

## WHERE ARE THE RESPONSE HEADERS?!
It took me hours of combing through different blog posts, guides, tutorials and the like to get to this point, as each different one assumed I knew how to do something different that they just didn't explain. But when I did, I thought something was wrong, because there were no headers! I fiddled every which way with my CORS settings, my request headers, my controllers, my Devise settings, everything I could think of, but I just couldn't find any headers in the responses.

I was primarily operating through fetch requests, like the following:
```
fetch(`http://localhost:3000/users/`, { 
  method:  'POST',
	headers: {
    "Content-Type": "application/json",
    "Accept": "application/json"
  },
	body: JSON.stringify( {
	  user: {
		  email: user.email,
			password: user.password
	  }
	})
	.then(response => {
	  response.headers // THIS IS EMPTY! WHY, WHERES MY 'AUTHORIZATION' HEADER???
		return response.json()
	})
	.then(json => {
    processJson(json) // I receive my logged in user / new user, so I know the request worked, what's happening?
	}
```
After tireless searching, I eventually found an answer in a side comment on a StackOverflow post that no one else confirmed as the correct way: the reponse headers have a 'get' method that will retrieve the specific header you're looking for, if it exists.

It works like this:
```
response.headers.get('Authorization')
```
Or in context:
```
fetch(`http://localhost:3000/users/`, { 
  method:  'POST',
	headers: {
    "Content-Type": "application/json",
    "Accept": "application/json"
  },
	body: JSON.stringify( {
	  user: {
		  email: user.email,
			password: user.password
	  }
	})
	.then(response => {
	  token = response.headers.get('Authorization') // => 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwic2....
		return response.json()
	})
	.then(json => {
    processJson(json)
  })
```

## We did it!
So, there you have it, that's how authentication with devise-jwt works. Happy coding!

