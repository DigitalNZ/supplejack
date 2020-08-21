---
layout: page
title: "Supplejack Client"
category: components
date: 2014-06-30 15:00:53
order: 5
---

The Supplejack Client is a library to abstract the interaction with the Supplejack API. It connects to the Supplejack API, and allows you to treat models as if they were in a local database.

For more information on how to configure and use this application refer to the [documentation](http://digitalnz.github.io/supplejack).

## Installation

Add it to your Gemfile:

```ruby
gem 'supplejack_client', git: 'https://github.com/DigitalNZ/supplejack_api.git'
```

Run bundle install:

```ruby
bundle install
```

Run the installation generator:

```ruby
rails g supplejack:install
```

An initializer was created at `config/initializers/supplejack_client.rb`

You should set the variables needed by the Supplejack API:
- Api Key
- Api URL

To start using Supplejack gem you have add the following line to any plain ruby class

```ruby
class Record
  include Supplejack::Record
end
```

Then do `Supplejack::Search.new(params)` or `Record.find(id)`

## Search params

|------------+---------------+---------|
| Param name | Default value | Details |
| ---------- | ------- | -------- |
| text | '' | Text to be searched |
| geo_bbox | `not defined` | A geographic bounding box scoping a search to a geographic region. Order of latitude-longitude coordinates is north, west, south, east. The following example searches the Wellington region: `&geo_bbox=-41,174,-42,175` |
| record_type | 0 | **DNZ specific** natlib to search through its 'authorities'. Normal records are 0, authorities are 1. |
| page | 1 | Page to display |
| per_page | `Supplejack.per_page` | Items per page |
| and | [ ] | `and` for filter results from search |
| without | [ ] | `without` filters to exclude results from search |
| facets | `not defined` | Array of facets to display |
| facets_per_page | 0 | Number of facets per page |
| fields | `Supplejack.fields` | Fields to be returned |
| query_fields | `not defined` | All fields to be searched by text |
| ignore_metrics | `not defined` | If set, api requests will be ignored from metrics |
| solr_query | `not defined` | ? |
| sort | `not defined` | Define a facet to be ordered by |
| direction | `asc` | Define a sort direction |
| exclude_filters_from_facets | `false` | Set this value to true in order to exclude filters from facets so you can retain facets values while drilling down the search |
|-----------------------------+---------+-------------------------------------------------------------------------------------------------------------------------------|


## More like this
For this to work `[:mlt]` should be configured to a field in the Record Schema.

Then do  `Supplejack::MoreLikeThis.new(record_id, options).records`. This will return an array of SupplejackRecords.

`record_id` is available on SupplejackRecord in `.record_id`.
`options` can include the fields with which the relatedness is queried for and the frequency.
Frequency is the number of times words should show up in a record filed to make the similarity relation between them.

Example `{ mlt_fields: [:title, :description], frequency: 2}`, default frequency is 1.


## Configuration

Below is the sample `config/initializers/supplejack_client.rb` file that comes with supplejack website. You need to modify it if you make any changes in supplejack record schema.

#### Notes:

* It is very important that you specify **config.fields** in `supplejack_client.rb` correctly. The fields must be declared in supplejack api record schema.
* The **config.facets** list facets to be returned from the api. The facets must be declared in supplejack api record schema.
* Supplejack demo website and supplejack api are highly dependent on each other. For example: In supplejack website 's `supplejack_client.rb` file, **config.facets** lists `category`. `category` facet is used to build search tab in supplejack website, check out [Github code](https://github.com/DigitalNZ/supplejack_website/blob/master/app/models/search_tab.rb#L26). It is declared in supplejack api record schema as a facet. If you want to rename `category` differently eg `type`, you need to update the code accordingly.
* For a full list of configuration options, see the [base config](https://github.com/DigitalNZ/supplejack_client/blob/master/lib/supplejack/config.rb)

```ruby
Supplejack.configure do |config|

  # ===> Credentials
  # Use the api_key for your Supplejack user
  # Please replace XXXX with your own api key
  config.api_key = API_KEY
  #
  # ===> End point
  # For production use default url which is http://api.youapihost.org
  config.api_url = API_HOST
  #
  # ===> URL Format
  # This is the format use for the url's in the application
  # The default is the item hash which looks like: "text='dog'&i[content_partner]=NLNZ&i[type]=Images"
  # config.url_format = :item_hash
  #
  # ===> Facets
  # The is the list of facets that are going to be requested to the api
  # When you ask for the facets, they are going to be ordered in the
  # order presented here
  config.facets = [
    :category
  ]
  #
  # ===> Facet values sorting
  # By default facet values are sorted by whatever solr returns.
  # The sorting options are :index and :count
  # :index means lexical sorting (Alphabetical)
  # :count means is ordered by the number of results for each facet value
  # config.facets_sort = nil
  #
  # ===> Fields
  # This is a list of fields/groups that will be requested to the API for every
  # record. :default will return the default set of fields.
  #
  config.fields = [
    :default,
    :source_contributor_name,
    :source_website_name,
    :subject
  ]

  # ===> Number of facet values
  # This will limit the number of facet values returned for each facet
  # Be carefull not to make it too high for performance reasons
  # config.facets_per_page = 10
  #
  # ===> Per Page
  # Number of results returned per page
  # config.per_page = 20
  #
  # ===> Timeout
  # By default the request to the API will timeout after 30 seconds
  # config.timeout = 30
  #
  # ===> Single value methods
  # Some of the values returned by the API are actually multiple values
  # so they are returned as a array. But most of the time we are only intereseted
  # in one of those values. Here you can define which values would you like to
  # be converted to a string.
  # config.single_value_methods = [
  #   :email
  # ]
  #
  # ===> Search attributes
  # The search object can store any number of attributes which are actually
  # the filters passed to the search
  # Here you can define which attributes you want the search to accept.
  # This is going to allow you to do:
  #   search = Search.new(text: 'dog', i: {type: 'Images'})
  #   search.type
  #
  config.search_attributes = [
    :subject
  ]
  #
  # ===> Record klass
  # Name of the main model throught which you interact with the Supplejack API
  # This is used to initialize objects of this class when the list of
  # favourites is fetched.
  #
  # config.record_klass = "Record"
  #
  # ===> Enable Debugging
  # Set this flag to true in order to get display errors and the actual SOLR requests
  # in the logs generated by the API.
  #
  config.enable_debugging = true
  #
  # ===> Enable Caching
  # Set this flag to true in order to cache the facet_value response and the search counts
  #
  config.enable_caching = false
  #
  # ===> Enable Request Logging for Usage Metrics
  # Set this flag to true in order to enable logging
  #
  config.request_logger           = true
  # Specify which field from the record model is to be logged
  config.request_logger_field     = :primary_collection  
end
```
