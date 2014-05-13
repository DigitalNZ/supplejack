---
layout: page
title: "Introduction to parser scripts"
category: manager
date: 2014-05-06 11:53:13
order: 1
---


Parser scripts are instructions that describe how data from a particular source is to be harvested into Supplejack. 

View the [introductory screencast](http://youtu.be/MLUURxcfcLc) for a general overview of how parser scripts work inside Supplejack. 

You can also view the introductory tutorials that will take you through a step by step process in creating your first parser scripts [Tutorial 1](https://drive.google.com/file/d/0B63EYVIeMWSfdThwRXhxcllwTVE/edit?usp=sharing) [Tutorial 2](https://drive.google.com/file/d/0B63EYVIeMWSfdERXYTJJYmR2cW8/edit?usp=sharing)

### What does a parser script look like?

```ruby 
class PublicAddressAndy < HarvesterCore::Rss::Base

  # Specify the location of the data
  
  base_url "http://publicaddress.net/system/6.rss"

  # Default mappings - the same attributes value for all records

  attributes :content_partner, :display_content_partner, default: "Public Address"
  attributes :category, default: "Podcasts"
  attributes :primary_collection, :display_collection, :collection, default: "Public Address Radio"
  attributes :usage, default: "All rights reserved"
  
  # Variable mappings - different attribute values for each record
  
  attributes :title, xpath: "/item/title"
  attributes :description, xpath: "item/description"
  attributes :landing_url, xpath: "item/link"
  attributes :display_date, xpath: "item/pubDate"
  attributes :date, xpath: "item/pubDate", date: true
  attributes :category, xpath: "item/pubDate"
  
  attributes :internal_identifier do
    get(:landing_url).downcase
  end

  
end
```

The above example of a simple parser script starts by identifying the source of the data to ingest from (base_url). Each script then identifies the data fields (attributes) to copy data into. Field names are based on the specific schema that has been set up for your Supplejack instance. 

Supplejack is supported by a [Parser DSL](/supplejack/manager/parser-dsl-domain-specific-language.html) (Domain Specific Language) that has been designed specifically to support the capture and manipulation of data. The Parser DSL provides rich functions for getting, namespacing, validating, transforming, and enriching your source data. Supplejack allows you to use xpath expressions and regex to get the data, as well as providing the option for ruby code if you need to do some heavy lifting.

The tutorials above are a great place to start in understanding how to build your first parser script, with the Parser DSL, and example scripts being the place to look for advanced support.

