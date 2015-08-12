---
layout: page
title: "Supplejack Website"
category: start
date: 2014-06-30 15:00:53
order: 7
---

The Supplejack Website is part of the standard Supplejack platform that can aggregate data and content from many different sources.

It uses the `supplejack_client` gem to connect to the Supplejack API. For more information about Supplejack Client gem, please refer to the [documentation](http://digitalnz.github.io/supplejack/start/supplejack-client.html#configuration)

## Installation

Clone the project

```bash
git clone git@github.com:DigitalNZ/supplejack_website.git
```

Create a `local_env.rb` file and place it inside `config/`

```ruby
# config/local_env.rb
API_HOST = 'http://localhost:3000'
API_KEY = 'your_api_key'
THUMBNAIL_SERVER_URL = "http://magickly.afeld.me/'
```

Run bundle install

```bash
bundle install
```

Run Rails server

```bash
rails s
```

####Notes

* Supplejack demo website should work out of the box, providing no modification in supplejack api record schema.
* You need to update `config/initializers/supplejack_client.rb` if you modify the sample supplejack api record schema. Refer to [Supplejack Client](/supplejack/start/supplejack-client.html) doc to understand the configuration steps.
* Please always check [Website github](https://github.com/DigitalNZ/supplejack_website) source code to know what to fix.
