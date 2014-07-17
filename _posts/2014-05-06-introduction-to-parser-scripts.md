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

### Concept parser script

Below is an example of a parser for harvesting concepts. Make sure that your Manager is [configured](/supplejack/manager/concepts-configuration.html) to harvest concepts.

```ruby
class AucklandArtGalleryPeopleConcepts < SupplejackCommon::Xml::Base
  
  # Issues
  # http://49.50.242.36/search.do?view=detail&db=person&keyword=Katsukawa+Shunko
  
  base_url "file:////data/arts.xml"

  record_selector "//vernon"
  record_format :xml
  
  match_concepts :create_or_match
  
  attributes :internal_identifier, :landing_url do 
    compose("http://aucklandart.govt.nz/Person/", fetch("//person_id"))
  end
  
  attributes :type,                       default: "foaf:person"
  
  attributes :label,                      xpath: "//display_name"
  attributes :name,                       xpath: "//display_name"
  attributes :dateOfBirth do
    fetch("//birth_date").to_date
  end
  
  attributes :dateOfDeath do
    fetch("//death_date").to_date
  end

  attributes :givenName do
    fetch("//display_name").split(" ").first
  end
  
  attributes :familyName do
    fetch("//display_name").split(" ").select(:last)
  end
  
  attributes :gender do
    fetch("//person_type").split("|").select(2).downcase
  end
  
  attributes :sameAs do
    compose("http://www.aucklandartgallery.com/the-collection/browse-artists/", fetch("/person/@ext_id").first) # this page has a redirect
  end
  
  attributes :placeOfBirth do
    fetch("//birth_place")
  end
  
  attributes :placeOfDeath do
    fetch("//death_place")
  end
  
  reject_if do
    not (get(:dateOfBirth).present? and get(:dateOfDeath).present? and get(:familyName).present? and get(:givenName).present?)
  end 

end
```

The above example will posts data to the API with concept schema [configured](/supplejack/api/creating-schemas.html).