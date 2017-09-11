---
layout: page
title: "Supplejack API"
category: start
date: 2014-05-01 15:00:53
order: 3
---

The Supplejack API is a [mountable engine](http://guides.rubyonrails.org/engines.html) that provides functionality to store, index and retrieve metadata via an API. It also includes an API dashboard for monitoring API key activity and setting query throttle rates.

This wiki provides a guide on how to create and configure your API as well as an introduction to the rest of the Supplejack project.

## Getting started

Before starting you should check that you have all the [dependencies](/supplejack/start/dependencies.html) installed.

Once that is complete we strongly recommend using the [Supplejack template](https://github.com/DigitalNZ/supplejack_installation) to create your app. This template will create a new app which includes the Supplejack API engine and then step through the configuration options. 

For details about how to install Supplejack Template, see [Install & Setup](/supplejack/start/install-setup.html)

Once the install is complete you should have a working API. The next step is to [configure your schema](/supplejack/api/creating-schemas.html) so that you can configure the fields that are stored/returned by your API.

## Setup a custom record model from your API [OPTIONAL]

If you want to use a custom SupplejackApi::Record model, you can define a supplejack_api.rb initializer file and define the following block:

```ruby
SupplejackApi.setup do |config|
  config.record_class = YourCustomClass
  config.preview_record_class = YourPreviewCustomClass
end
```

Make sure your model follow the same structure as the [SupplejackApi::Record](https://github.com/DigitalNZ/supplejack_api/blob/master/app/models/supplejack_api/record.rb) and [SupplejackApi::PreviewRecord](https://github.com/DigitalNZ/supplejack_api/blob/master/app/models/supplejack_api/preview_record.rb)

By setting a custom class you will be able to do for example: YourCustomClass.custom_find(<id>)

### Generate API User keys

From the Worker's project root, Run the console.

```ruby
rails c
```

Create a user.

```ruby
 > SupplejackApi::User.create(email: 'your@email.com', name: 'Your Name', username: 'your_username')
=> #<SupplejackApi::User _id: 53d58681f694195642000002, created_at: 2014-07-27 23:08:49 UTC, updated_at: 2014-07-27 23:08:49 UTC, email: "your@email.com", encrypted_password: nil, name: "Your Name", username: "your_username", sign_in_count: nil, current_sign_in_at: nil, last_sign_in_at: nil, current_sign_in_ip: nil, last_sign_in_ip: nil, authentication_token: "JmVe15z2BSHDwaVsjMvA", daily_requests: 0, monthly_requests: 0, max_requests: 10000, role: "developer", daily_activity: nil, daily_activity_stored: true>
 > SupplejackApi::User.last.authentication_token
=> "RhymLHa9xRQGU8gyAYXP"
```