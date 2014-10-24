---
layout: post
title: "Building a tested, documented and versioned JSON API using Rails 4"
date: 2013-05-18 18:47
comments: true
categories: [rails, api]
---
## Goals

1. Use the leaner [rails-api](https://github.com/rails-api/rails-api). This removes a lot of stuff Rails that you don't need for an API. This ensures that the API works for non-browser clients which do not support cookies. Also, there is no "View" layer that renders an HTML view for every request. This makes the response faster.
2. Use Cucumber for integration tests. This serves as a way for testing the APIs and also for documenting them.
3. Use devise gem for authentication and support token based authentication. There are things to watch out here since devise by default requires session and also tries to redirect after requests. We need to get around these.
4. Version API's as per best practices.

### rails-api

Setting up Rails::Api is fairly straightforward.

1. Add `rails-api` to the Gemfile and `bundle install` .
2. ApplicationController should inherit from `ActionController::Api` which doesn't include many modules that are not required for an API.

### Cucumber for testing JSON API's

Cucumber is a Behavior driven development tool and by default it ships with "capybara" which simulates a user's browser actions like filling up forms or clicking elements on the page. We need to change this behavior and simulate an external client that talks to the API directly using HTTP requests and understands JSON.

Since authentication API's accept username and password in HTTP BasicAuth headers, cucumber needs support for that as well. I found this nice little gem which helps here : [cucumber-api-steps](https://github.com/jayzes/cucumber-api-steps) . It helps you write steps in cucumber that simulate an external client talking to the API and supports HTTP BasicAuth.

Now the problem comes while asserting the correctness of responses ( that's the whole point of testing ). I found another gem that helps here : [json_spec](https://github.com/collectiveidea/json_spec) . It ships with cucumber steps that help you match the response to your expected response .

The result is a well tested API whose test output serves as documentation for the API. Here's a sample test output : 

```ruby
Feature: Sign In

    Background:
      Given I send and accept JSON

    Scenario: Successful sign in using email and password
      Given "Adam" is a user with email id "user@gmail.com" and password "password123"
        And his role is "admin"
        And his authentication token is "auth_token_123"
      When I authenticate as the user "user@gmail.com" with the password "password123"
      And I send a POST request to "/api/users/sign_in"
      Then the response status should be "200"
      And the JSON response should have "auth_token"
        And the auth_token should be different from "auth_token_123"
      And the JSON response at "user_role" should be "admin"
```

### Devise for authentication.

This is where things get tricky. First of all, devise needs to work well with Rails 4. There's a `rails4` branch in devise github repo which I specified in my Gemfile.

Devise doesn't work out of the box for a Rails app built on `rails-api`. That's because devise requires MimeResponds module which is not included when we use `rails-api`,
also `rails4` branch of devise calls the "permit" method on params. This won't work because ApplicationController inherits from ActionController::API
 when using `rails-api` and does not include StrongParameters and params will be an instance of HashWithIndifferentAccess which does not have
 the `permit` method. To solve these problems, add the following to `app/controllers/application_controller.rb`

{% highlight ruby %}
include ActionController::MimeResponds
include ActionController::StrongParameters
{% endhighlight %}

Also, devise has the default behavior of redirecting to different paths after authentication actions like sign_in or sign_up. Since we're buiding an API, which has no redirects, we should get around this. For this, I added my own `CustomDevise::SessionsController` which inherits from `Devise::SessionsController` to override the `create` method so that it doesn't redirect on successful sign in. This is how the custom controller looks like :

{% gist emilsoman/5604222 %}

This works for successful authentication. But if an authentication error occurs, the control goes to `warden` which uses a `failure_app` which is nothing but a Rack application which sets the flash messages and renders/redirects based on the requested format. I need a custom json response with an `errors` key holding an array of errors instead of the default `error` key. So I added a `custom_auth_failure_app.rb` under `config/initializers` with the following content :

<script src="https://gist.github.com/emilsoman/5604254.js"></script>

Now we have to tell Devise to use the custom failure app by adding this to `config/initializers/devise.rb` : 

{% highlight ruby %}
  config.warden do |manager|
    manager.failure_app = CustomAuthFailure
  end
{% endhighlight %}

#### Token based authentication

Here's the plan for token based authentication:

1. Client signs in using email and password, which are sent with [HTTP Basic Authentication headers](http://en.wikipedia.org/wiki/Basic_access_authentication)
2. Successful sign in resets the authentication token and sends it back in the response
3. Client uses this authentication token for every subsequent request.

In order to do this, we need to make a few changes.

1. Enable "token_authenticatable" in the devise model ( in our case, User model) and add the devise fields for token authentication in users table.
2. Set the following lines in `config/initializers/devise.rb` : 

{% highlight ruby %}
config.http_authenticatable = true
config.skip_session_storage = [:http_auth, :token_auth]
{% endhighlight %}

This will enable http basic authentication in devise and prevent session storage for token authentication.

There's another problem that I ran into. Devise uses the method `warden.authenticate!` to authenticate the user. This method by default tries to find a user from the Session. Since session is "nil" because `rails-api` removed it, it's going to throw an exception. You can get around this by including session, but the purpose of using Rails-Api is to make the app leaner. `warden-1.2.1` does not work with nil session. A few pull requests have gone into warden including one from me, that allows warden to be used without a session, and this has been released as `warden-1.2.2`. In order to use this version of warden, add `gem 'warden', '1.2.2'` before you add the devise gem in your Gemfile.

Now my cucumber scenarios for authentication are green. Phew !

### Versioning the API

There are many ways to do this, you can use gems like Versionist or replace Rails-Api with RocketPants which has a DSL to write your API controllers which takes care of versioning as well, or roll your own versioning. I decided to write my own, the excellent [RailsCast on Versioning APIs](http://railscasts.com/episodes/350-rest-api-versioning) is all you need.

So basically, you add the following to your routes :

1. "api" namespace : so that your api URI's look like : `http://domain/api/method`
2. scope with the version module : so that you can group your api version under a module, but the client doesn't need to add the version to the URI.
3. A constraint to the scope : so that a version can be specified in the "Accept" header as per best practices ( atleast GitHub does it this way ) and a default version can be used if the header is not present.

So after versioning, you end up with the following directory structure for controllers, specs and features : `/api/<version>/` , which helps you to maintain the versions separately.

There you go, by now you should have :

1. Leaner and supposedly faster Rails 4 app using rails-api
1. Tests using cucumber + step definitions for JSON API's
2. Documentation from cucumber output
3. Authentication using devise + warden >= 1.2.2
