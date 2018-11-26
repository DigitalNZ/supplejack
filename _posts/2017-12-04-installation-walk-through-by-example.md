---
layout: page
title: "Installation Walk-through by Example"
category: start
date: 2017-12-04 14:57:08
order: 11
---

## Goal

By the end of this tutorial, you will have a working set up of Supplejack on your local machine. Note that these instructions are intended to be run on either Mac or Linux.

### Step One - Setting up your working directories:

Create a folder called `supplejack` on your machine. Inside of this folder, clone down the following projects.

https://github.com/DigitalNZ/supplejack_manager as 'manager'. https://github.com/DigitalNZ/supplejack_worker as ‘worker’.

### Step Two - Setting up the Manager:

The Manager and Worker need a key to communicate with each other, the instructions below will tell you how to create them.

cd into your manager directory and run `bundle install`. This will install all of the dependencies of the application.

Once this has finished, copy the `config/application.yml.example` to `config/application.yml`. This file contains the environment variables that your manager needs to run. Looking at this file will show you the ports that the other applications in the app need to run on, and it is where you add the keys.

Now, boot up the rails console `rails c` and run `User.create(email: 'your@email.com', name: 'Your Name', password: 'yourpassword', password_confirmation: 'yourpassword', role: 'admin')`. These details are also what you will use to login to the Manager web interface.

**Make sure you make yourself an admin otherwise you will not be able to create parser scripts**.

This will create your user that you will use to login to the manager.

Now you will need to generate the worker key.

Run `bundle exec rake secret`

Write this key down, it is the **WORKER_KEY**

### Step Three - Setting up the Worker

As with the manager, cd into your worker directory and run bundle install.

Once this has finished, copy the `config/application.yml.example` to `config/application.yml`.

### Step Four - Sharing keys and Booting up the Manager and Worker.

#### For the Manager:

Add your **WORKER_KEY** to the `manager/config/application.yml` where it states **WORKER_KEY**. You will need to add this for each environment listed in this file.

#### For the Worker:

Add your **WORKER_KEY** to the `worker/config/application.yml` in the specified place. You will need to do this for the test, development, and staging environments.

*You can now boot up the Manager and the Worker*

** Make sure Mongo (port 27017)  and Redis server (port 6379) are running **

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

First you will need to install Rails 5.

`gem install rails --version=5.1.4`.

Because we are using an older version of rails, the most simple way to get started is like this:

* create a new folder called api, inside of the folder create a `Gemfile`.
* In the Gemfile, add the following:
* `source 'https://rubygems.org'`
* `gem 'rails', '5.1.4'`
* run `bundle install`.

Once it has finished, run `bundle exec rails new . --force --skip-bundle`.

Now, open up the app in your text editor and add `gem 'supplejack_api', git: 'https://github.com/DigitalNZ/supplejack_api.git'` to the Gemfile.

Once you have done this, run `bundle install`. If there are conflicts, run `bundle update` and they should resolve themselves.

Next, we can run the supplejack installer with `bundle exec rails g supplejack_api:install`. Press Y for any questions that it asks you. This will generate the extra files that your app needs to be a working Supplejack API.

Now you need to create the file `config/initializers/supplejack_api.rb` and add the following block of code.

```Ruby
SupplejackApi.setup do |config|
  config.record_class = SupplejackApi::Record
  config.preview_record_class = SupplejackApi::PreviewRecord
end
```

You also need to remove this line:

`protect_from_forgery with: :exception`

from your `app/controllers/application_controller.rb`

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

#### Step Six

Log into your manager and click ‘New Data Source’, give it a **name** and a **contributor**.

Once it completes, hover over ‘Contributors and Scripts’ in the top navigation, click ‘Parser Scripts’, and then click ‘New Parser Script’.

Name your parser, `Otago Hocken`, select the contributor and data source that you created before, and then choose OAI format.

The click ‘Create Parser Script’.

You will now have a screen which will allow you to write your parser script for the harvester, this parser is written in Ruby and has it’s own DSL, which is documented at the link that I provided earlier.

Make your parser look like this:


```ruby
class OtagoHocken < SupplejackCommon::Oai::Base
  base_url "http://otago.ourheritage.ac.nz/oai-pmh-repository/request"
  validates :usage, inclusion: {in: ["Share", "Modify", "Use commercially", "All rights reserved", "Unknown"]}

  validates :landing_url, format: {with: /\Ahttps?:/}
  validates :thumbnail_url, format: {with: /\Ahttps?:/}
  validates :large_thumbnail_url, format: {with: /\Ahttps?:/}
  validates :landing_url, size: { is: 1 }
  validates :internal_identifier, size: { is: 1 }

  namespaces dc: 'http://purl.org/dc/elements/1.1/',
  			 oai_dc: 'http://www.openarchives.org/OAI/2.0/oai_dc/',
  					xsi: 'http://www.w3.org/2001/XMLSchema-instance',
  			dcterms: 'http://purl.org/dc/terms/',
  						o: 'http://www.openarchives.org/OAI/2.0/'

  attributes :content_partner, :display_content_partner,     default: "University of Otago"
  attributes :display_collection, :primary_collection,       default: "Otago University Research Heritage"
  attribute  :collection,															       default: ["Otago University Research Heritage"]
  attributes :copyright, :usage,                             default: "All rights reserved"
  attributes :dc_rights, :rights_url,                        default: "http://digital.otago.ac.nz/terms.php"
  #attribute :dc_type, default: "Watercolors"

  attribute :title,         xpath: "//dc:title"
  attribute :description,   xpath: "//dc:description"
  attribute :date,          xpath: "//dc:date",        date: true
  attribute :display_date,  xpath: "//dc:date"
  attribute :contributor,   xpath: "//dc:contributor"
  attribute :publisher,     xpath: "//dc:publisher"
  attribute :subject,	      xpath: "//dc:subject"
  attribute :source,	      xpath: "//dc:source"
  attribute :creator,	      xpath: "//dc:creator"
  attribute :dc_type,	      xpath: "//dc:type"
  attribute :format,	      xpath: "//dc:format"

  attribute :category do
    category = "Images"
    category = "Videos" if get(:dc_type).find_with(/^Video$/).present?
    category
  end

  attributes :landing_url do
    fetch("//dc:identifier").find_with(/^http:\/\/otago.ourheritage.ac.nz\/items\/show/)
  end

  attribute :internal_identifier do
    get(:landing_url).downcase
  end

  attributes :large_thumbnail_url do
    fetch("//dc:identifier").find_with(/\.jpg$/).mapping(/original/ => 'fullsize').first
  end

  attributes :thumbnail_url do
    get(:large_thumbnail_url).mapping(/fullsize/ => 'square_thumbnails')
  end

  attribute :dc_identifier do
    dcidentifier = get(:dc_identifier)
    dcidentifier += fetch("//header/identifier")
    dcidentifier += fetch("//metadata//identifier").find_without(/^http/)
    dcidentifier
  end

  reject_if do
    not get(:landing_url).find_with(/^http/i).present?
  end
end
```

You should now be able to preview your parser! Click the preview button and the various components of Supplejack will whurr into a working state.

If you get an error such as ‘Unable to connect to localhost:3001 over (1)’ you will need to change the `config/application.yml` of the MANAGER and the WORKER to have the API HOST, MANAGER HOST, and WORKER HOST, as `http://127.0.0.1:port` instead of `http://localhost:port`.

If you get an error such as `Errno::ECONNREFUSED: Failed to open TCP connection to 127.0.0.1:3001 (Connection refused - connect(2) for "127.0.0.1" port 3001)` when previewing / harvesting, add the `-b 0.0.0.0` option to the `rails s` command when running the stack locally.

If you do not get any records coming through in your preview, look in the Sidekiq pane of your terminal. If there is an error about not being able to find a Record with a specific ID, you will have a mismatch within the `Record` and `PreviewRecord` IDs. The best thing to do here is to go into the API project, run the rails console and delete the `SupplejackApi::Record` and `SupplejackApi::PreviewRecord` models.

You can do this with `SupplejackApi::Record.destroy_all` and `SupplejackApi::PreviewRecord.destroy_all`

If your preview is successful, you will see a modal window appear a json object of the potential records. Something like this:

```json
{
  "priority": 0,
  "match_concepts": null,
  "content_partner": [
    "University of Otago"
  ],
  "display_content_partner": [
    "University of Otago"
  ],
  "display_collection": [
    "Otago University Research Heritage"
  ],
  "primary_collection": [
    "Otago University Research Heritage"
  ],
  "collection": [
    "Otago University Research Heritage"
  ],
  "copyright": [
    "All rights reserved"
  ],
  "usage": [
    "All rights reserved"
  ],
  "dc_rights": [
    "http://digital.otago.ac.nz/terms.php"
  ],
  "rights_url": [
    "http://digital.otago.ac.nz/terms.php"
  ],
  "title": [
    "Gog and Magog, Stewart Island. 1880."
  ],
  "description": [
    "Lower left (l.l.) in ink: W. Deverell Jany 1880; margin below image in pencil: Gog &amp; Magog Stewart Island."
  ],
  "date": [

  ],
  "display_date": [

  ],
  "contributor": [

  ],
  "publisher": [

  ],
  "subject": [
    "Islands",
    "Landscape"
  ],
  "source": [
    "Found uncatalogued in Hocken 1947. Dr T.M. Hocken’s Collection"
  ],
  "creator": [
    "unknown"
  ],
  "dc_type": [
    "Image",
    "Still Image",
    "Watercolors",
    "Art"
  ],
  "format": [

  ],
  "category": [
    "Images"
  ],
  "landing_url": [
    "http://otago.ourheritage.ac.nz/items/show/4480"
  ],
  "internal_identifier": [
    "http://otago.ourheritage.ac.nz/items/show/4480"
  ],
  "large_thumbnail_url": [
    "http://s3.amazonaws.com/ourheritagemedia%2Ffullsize%2Fa24297b36e4d17d4b8bd4da8f7d6bb6c.jpg"
  ],
  "thumbnail_url": [
    "http://s3.amazonaws.com/ourheritagemedia%2Fsquare_thumbnails%2Fa24297b36e4d17d4b8bd4da8f7d6bb6c.jpg"
  ],
  "dc_identifier": [
    "oai:otago.ourheritage.ac.nz:4480"
  ],
  "source_id": "otago-hocken",
  "data_type": "record"
}
```

If you have a successful preview, close the modal window, scroll to the bottom of the page, enter some text in the message field, and click Update Parser Script.  Next, in the righthand columns, click on the name of your parser script under **History**, then click the blue button **Tag as Staging**.

You can now run a staging harvest, note that ‘Staging’ is a term bound to the Manager and does not relate to the Rails environment.

Click ‘Run Harvest’, and then click ‘Staging Harvest’, enter 1000, and then click start. I do not know how large this example harvest is.

### Step Seven - Indexing your Records

Once the harvest has finished you are now ready to index your records into Solr. On our DNZ servers we have a background job that handles this, but since we are running locally we can just do it in the Rails console of the API.

Cd into your api repository, enter the rails console and run the following.

```Ruby
Sunspot.session = Sunspot::Rails.build_session
Sunspot.index(SupplejackApi::Record.all)
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

### Step Eight - Using the supplejack client to pull data from your API

Create a new Rails application, this can be Rails 5 if you would like.

`rails new my_app_name`

Add the supplejack_client to your Gemfile.

gem 'supplejack_client', git: 'https://github.com/DigitalNZ/supplejack_client.git'
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
<h1>Otago Hocken</h1>
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

You will be able to see the harvested content! There will be no styling but feel free to template up the site and make it look how you want.
You can search against the API by going to /records?text=your_text_string

The API also supports pagination, so you can pass &per_page=number and &page=number to do this. You can find out the total number of items on the @search object itself, it has a method ‘total’.

You now have every element of the Supplejack stack working together correctly, awesome job!
