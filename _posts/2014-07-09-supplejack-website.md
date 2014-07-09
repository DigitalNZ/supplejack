---
layout: page
title: "Supplejack Website"
category: start
date: 2014-06-30 15:00:53
order: 7
---

The Supplejack Website is part of the standard Supplejack platform that can aggregate data and content from many different sources.

It uses the `supplejack_client` gem to connect to the Supplejack API. For more information about Supplejack Client gem, please refer to the [documentation](http://digitalnz.github.io/supplejack/start/supplejack-client.html)

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
THUMBNAIL_SERVER_URL = 'http://thumbnails.digitalnz.org'
```

Run bundle install

```bash
bundle install
```

Run Rails server

```bash
rails s
```