---
layout: page
title: "Example Project"
category: start
date: 2017-12-04 14:57:08
---

## Goal

By the end of this tutorial, you will have a working set up of Supplejack on your local machine. Note that these instructions are intended to be run on either Mac or Linux.

### Step One - Setting up your working directories:

Create a folder called `supplejack` on your machine. Inside of this folder, clone down the following projects.

https://github.com/DigitalNZ/supplejack_manager as 'manager'. https://github.com/DigitalNZ/supplejack_worker as ‘worker’.

### Step Two - Setting up the Manager:

The Manager and Worker need API keys to communicate with each other, the instructions below will tell you how to create them.

cd into your manager directory and run `bundle install`. This will install all of the dependencies of the application.

Once this has finished, copy the `config/application.yml.example` to `config/application.yml`. This file contains the environment variables that your manager needs to run. Looking at this file will show you the ports that the other applications in the app need to run on, and it is where you add the API keys.

Now, boot up the rails console `rails c` and run `User.create(email: 'your@email.com', name: 'Your Name', password: 'yourpassword', password_confirmation: 'yourpassword', role: 'admin')`. These details are also what you will use to login to the Manager web interface.

**Make sure you make yourself an admin otherwise you will not be able to create parser scripts**.

This will create your user that you will use to login to the manager, the user also has the authentication_token that will need to be shared with the worker.

You can see this key by running `User.last.authentication_token` in the rails console. If you have multiple accounts, you can use `User.find_by(email: ‘your_email@email.com’).authentication_token` to find the token of a particular user.

Write this key down, it is the **MANAGER_API_KEY**.

### Step Three - Setting up the Worker

As with the manager, cd into your worker directory and run bundle install.

Once this has finished, copy the `config/application.yml.example` to `config/application.yml`.

Now boot up the rails console and run `User.create`. This will generate your worker user. There is no web interface so you do not need to enter a username or a password. You can get this key by running `User.last.authentication_token`.

Write this key down, it is the **WORKER_API_KEY** and will need to be shared with the manager.

### Step Four - Sharing keys and Booting up the Manager and Worker.

#### For the Manager:

Add your **WORKER_API_KEY** to the `manager/config/application.yml` where it states **WORKER_API_KEY**. You will need to add this for each environment listed in this file.

#### For the Worker:

Add your **MANAGER_API_KEY** to the `worker/config/application.yml` in the specified place. You will need to do this for the test, development, and staging environments.

*You can now boot up the Manager and the Worker*

Inside of the Manager directory, run `bundle exec rails s -p 3001`.

Inside of the Worker directory, run `bundle exec rails s -p 3002`. You will also need to run sidekiq. Sidekiq is responsible for processing the workers jobs. Inside of the worker directory, run `bundle exec sidekiq`.

The worker, manager and api ports  are configured in the `config/application.yml` file of each project.

To determine that everything is working correctly, visit the following:

The Manager: http://localhost:3001
You will be able to login here with the credentials you previously generated for the manager.

The Workers Sidekiq: http://localhost:3002/sidekiq
Here you will be able to see a visual representation of the queues that are being processed.

### Step Five - Setting up the API

The Supplejack API project on github is mountable rails engine. This means it is intended on being run inside of a host Rails application. Our first step will be creating the host application.

First you will need to install Rails, we need to use an older version as supplejack_api is not yet compatible with Rails 5.

`gem install rails --version=4.1.4`.

Because we are using an older version of rails, the most simple way to get started is like this:

* create a new folder called api, inside of the folder create a `Gemfile`.
* In the Gemfile, add the following:
* `source 'https://rubygems.org'`
* `gem 'rails', '4.1.4'`
* run `bundle install`.

Once it has finished, run `bundle exec rails new . --force --skip-bundle`.

Now, open up the app in your text editor and add `gem 'supplejack_api', git: 'https://github.com/DigitalNZ/supplejack_api.git'` to the Gemfile.

Once you have done this, run `bundle install`. If there are conflicts, run `bundle update` and they should resolve themselves.

Next, we can run the supplejack installer with `bundle exec rails g supplejack_api:install`. Press Y for any questions that it asks you. This will generate the extra files that your app needs to be a working Supplejack API.

Now, you will need to go into the Gemfile of the api and remove the **git** path from the *active_model_serializers* gem.

Now you need to create the file `config/initializers/supplejack_api.rb` and add the following block of code.

```Ruby
SupplejackApi.setup do |config|
  config.record_class = SupplejackApi::Record
  config.preview_record_class = SupplejackApi::PreviewRecord
end
```

Now you need to go into the `app/supplejack_api/record_schema.rb` file and add a new harvester role like so:

```ruby
role :harvester, harvester: true
```

Lastly, we need to configure the harvester key that allows the Worker and the Manager to communicate with the API. To do this, open up the rails console, with `rails console`, and create a user.

```Ruby
SupplejackApi::User.create(email: 'yourharvest@email.com', password: 'password', password_confirmation: 'password', role: 'harvester')
```

Note, make **sure** that you have the *harvester* role. Otherwise you will get an authentication failed error when you attempt to create content partners and run harvests.

Once the user has been made, copy it’s authentication_token and put it in the `config/application.yml` of each the Worker and the Manger where it says HARVESTER_API_KEY. Again, you will need to do this for *each* environment.

You are now ready to run the stack!

### Step Six - Running the Stack

#### Boot the API:

Cd into your api directory and boot it up on port 3000

`bundle exec rails s -p 3000`

#### Boot Solr

Cd into your API directory and run `bundle exec rails generate sunspot_rails:install`.  You will be asked if you wish to overwrite sunpot.yml?  Answer Yes.

If there is a solr directory, you will need to remove it `rm -rf solr`.

Now run:

`bundle exec rake sunspot:solr:run`

#### Boot the Manager

cd into your manager directory and run:

`bundle exec rails s -p 3001`

*Note if you are still running the manager from previous steps, you will need to restart it for the config changes to take effect. Kill the Rails app with ctrl-c.*

#### Boot the Worker

Cd into your worker directory and run:

`bundle exec rails s -p 3002`

*Note if you are still running the worker from previous steps, you will need to restart it for the config changes to take effect. Kill the Rails app with ctrl-c.*

#### Boot Sidekiq

Cd into your worker directory:
`bundle exec sidekiq`

You are now ready to set up your parser! Here we will make an example parser but once you have got it working.

The documentation for the parser DSL can be found here:

http://digitalnz.github.io/supplejack/

## ADD STEP SIX

If you have a successful preview, close the modal window, scroll to the bottom of the page, enter some text in the message field, and click Update Parser Script.  Next, in the righthand columns, click on the name of your parser script under **History**, thenclick the blue button **Tag as Staging**.

You can now run a staging harvest, note that ‘Staging’ is a term bound to the Manager and does not relate to the Rails environment.

Click ‘Run Harvest’, and then click ‘Staging Harvest’, enter 1000, and then click start. I do not know how large this example harvest is.

### Step Seven - Indexing your Records

Once the harvest has finished you are now ready to index your records into Solr. On our DNZ servers we have a background job that handles this, but since we are running locally we can just do it in the Rails console of the API.

Cd into your api repository, enter the rails console and run the following.

```Ruby
Sunspot.session = Sunspot::Rails.build_session
SupplejackApi::Record.all.map(&:index)
Sunspot.commit
```

**If you want to delete all of your records, you can run this. Do not run on production**

```Ruby
Sunspot.session = Sunspot::Rails.build_session
SupplejackApi::Record.destroy_all
Sunspot.remove_all
Sunspot.commit
SupplejackApi::PreviewRecord.destroy_all
```

You can now see your records on the API! Go to `http://localhost:3000/records.json?api_key=YOUR_API_KEY&fields=verbose,title,thumbnail_url,large_thumbnail_url` to see the serialized JSON response from Solr.

**Note the fields that are returned by default are determined by the groups on your RecordSchema. You can alter the your RecordSchema to whichever fields you would like, check out the docs here.**

Oliver: I don't think you mean this url:
`http://localhost:3000/records.json?api_key=devkey&fields=verbose,title,thumbnail_url,large_thumbnail_url`

### Step Eight - Using the supplejack client to pull data from your API

Create a new Rails application, this can be Rails 5 if you would like.

`rails new my_app_name`

Add the supplejack_client to your Gemfile.

gem 'supplejack_client', git: 'https://github.com/DigitalNZ/supplejack_api.git'
Then run `bundle install`

The default testing framework that Rails ships with is MiniTest, all of our projects use [RSpec](https://github.com/rspec/rspec-rails) to add it to the Gemfile and follow the configuration steps to get up and running.

Then you can run `rails g supplejack:install`. This will scaffold the basic things your app needs to communicate with your Supplejack API.

Now open up `config/initializers/supplejack_client.rb` and add your config.api_key and your config.api_url. The api key is generated the same was as the one you generated for the harvester. Refer back to Step 5 except this time you want to be either a developer or an admin user. `SupplejackApi::User.create(email: 'youruser@email.com', password: 'password', password_confirmation: 'password', role: 'admin')`. The harvester role is only for the worker and the manager to communicate with the API.

**The config.api_url will need to be uncommented.**

You also need to update the config.fields array to include the symbols `:verbose, :title, :thumbnail_url, :large_thumbnail_url, :record_id`.

This will ensure that we get some useful data through the API.

Now create a record model and include the Supplejack::Record class into it.  Don't use the `rails g model record` command to do this, as it will create a rails migration, which is unnecessary: this model won't inherit from ApplicationRecord like most models do.  Instead, Supplejack will govern the model behavior and link it to our api.

Like this:

```Ruby
class Record
  include Supplejack::Record
end
```

Now you can search the API in the rails console with `Supplejack::Search.new(params)` or find a specific record like `Record.find(record_id)`.

You can find a record_id by adding `record_id` to the list of fields in the api GET request you previously made in the browser.

### Step Nine - Showing your data on your host app

Create a new records_controller in your host application. You can do this with `rails g controller records`. The generate command will create the controller, the view, and the related spec files.

Note: if you need to undo your rails g command, you can run the same command again but using rails d instead of g.
Make your records controller look like this:

```Ruby
class RecordsController < ApplicationController
  def index
    @search = Supplejack::Search.new(params)
    @records = @search.results
  end

  def show
    @record = Record.find(params[:id])
  end
end
```

Update your `config/routes.rb` file to look like this:

`resources :records`

Now you can add two templates, one for the index page and one for the show page.
Make your `app/views/records/index.html.erb` file look like this

```ERB
<h1>Parser</h1>
<% @records.each do |record| %>
  <%= link_to(record.title, record_path(record.record_id)) %>
  <%= image_tag(record.thumbnail_url) %>
<% end %>
```

And your `app/views/records/show.html.erb` file look like this:

```ERB
<h1><%= @record.title %></h1>
<%= image_tag(@record.large_thumbnail_url) %>
<%= link_to('Back to records', records_path) %>
```

Now boot up your application, `bundle exec rails s -p 3005` and visit the /records page.

You will be able to see the harvested Flickr content! There will be no styling but feel free to template up the site and make it look how you want.
You can search against the API by going to /records?text=your_text_string

The API also supports pagination, so you can pass &per_page=number and &page=number to do this. You can find out the total number of items on the @search object itself, it has a method ‘total’.
