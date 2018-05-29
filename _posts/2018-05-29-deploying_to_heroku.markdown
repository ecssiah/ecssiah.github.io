---
layout: post
title:      "Deploying To Heroku"
date:       2018-05-29 21:15:18 +0000
permalink:  deploying_to_heroku
---


Heroku is a service that allows you to host your application publicly for free. This means your users can try out your application without needing to install it locally and build their own database. This post is a description of how to deploy a Rails app to Heroku.

The first thing you'll need is a [Heroku account](https://signup.heroku.com/login).

Next, you'll need to install the Heroku command line tools. You can get them directly from brew or grab the installer [off their site](https://devcenter.heroku.com/articles/heroku-cli):

```bash
brew install heroku/brew/heroku
```

Next, you'll want to log in to the command line interface using the Heroku account email and password you just set up. You can do this with `heroku login`.

At this point, you can `cd` into the root directory of your Rails app. Make sure that your Rails app already has an active Git repository, and then create a new Heroku app by using: `heroku create APP_NAME`, replacing APP_NAME with the name you want to deploy your application under. This will set up a new git remote named `heroku` that will allow you to push your application up to the Heroku servers.

There are a few things you have to do before pushing your code to Heroku. Heroku requires that your application runs on a postgres database, as opposed to the default sqlite3 database set up by Rails.

There are a couple steps involved with moving to postgres. It is not essential, but installing the [Postgres app](https://postgresapp.com/) for mac is helpful. It allows you to easily start up the postgres server and keep track of the databases you create. You'll also need to make sure you install postgres on your machine. This can also be done through brew with: `brew install postgresql`.

The first step in switching your application over is to remove the sqlite3 gem and replace it with the postgres gem.

```ruby
# Remove this
gem 'sqlite3'
# Add this
gem 'pg'
```

Now. create a postgres user account that will manage the databases for your app. This can be done through the command line with a tool that was installed by postgresql called `psql`. Running `psql` will log you into the default postgres user, and this will allow you to create a new user (or role) for this application.

Create a user for your application by running this command at the prompt provided by `psql`:

```postgres
CREATE USER app_name PASSWORD 'password';
```

Replace `app_name` and `password` with the name of your application and a password of your choice.

Next, you'll need to open `config/database.yml` in your application directory. This is the file Rails uses to configure your application's databases. It is automatically generated to use sqlite3, so a few changes have to be made. You have to tell Rails to use the postgres user information you just created.

Change the default configuration to use the postgresql adapter (make sure to get the full name including the 'ql' at hte end):

```ruby
default: &default
  adapter: postgresql	
	...
```

Now, update the configuration for each environment so that it uses the postgres user credentials you just created for your app.

```ruby
production:
  <<: *default
	database: app_name_production
	username: app_name
	password: password
```

Once again, replace `app_name` and `password` with your own information, and also add this info to the configurations for the other environments as well. It's possible to continue using sqlite3 for your test and development environments, but it is not recommended, so you might as well bite the bullet now and fully move to postgres. From the perspective of developing your app, there will be practically no differences, unless you absolutely need to write explicit SQL statements, but, even there, the differences are minimal.

The next thing you need to do is configure your Rails app to serve and precompile your static assets like images. This is done by simply adding a few lines to `config/application.rb` and `config/environments/production.rb`. This step ensures that Heroku will have access to these assets.

`config/application.rb`:
```ruby
# Replace this line
config.serve_static_assets = false
# With this line
config.serve_static_assets = true
```

`config/environments/production.rb`
```ruby
# Add these two lines
config.serve_static_files = true
config.assets.compile = true
```

Finally, you need to run `bundle install` and then `bundle exec rake assets:precompile RAILS_ENV=production` to install the postgres gem and precompile your static assets for Heroku.

You also need to run your migrations both locally and through the Heroku command line interface. You might need to drop your old database, and then run `rake db:migrate` to set up the new postgres database. You can run the same migrations for Heroku with `heroku run rake db:migrate`. Now, you should have your database and all your assets ready for Heroku.

The final step is to push your application to the Heroku remote. This is done exactly the same way you push to your own git remote.

```bash
git push heroku master
```

If this works without errors, then you should be able to see your application successfully deployed by viewing your Heroku account.

The last thing I'll mention is related to using OmniAuth in your application. You might remember that you needed to provide your OmniAuth provider with a client id and a client secret in order to authenticate users through their service. It is best practice to **not** push these credentials up to your application repository, because that would leave your application vulnerable. This means that Heroku will not immediately have access to this information, and that will prevent OmniAuth from working.

The solution is quite simple. You can either add these environment variables directly with the [Heroku dashboard at their site](https://devcenter.heroku.com/articles/config-vars) or you can use the command line interface. This is how you set them with the CLI:

```
heroku config:set KEY=VALUE
```

Once you've set these environment variables for Heroku, then OmniAuth should also work in your deployed app.







