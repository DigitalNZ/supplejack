---
layout: page
title: "Enrichments"
category: manager
date: 2014-05-01 16:11:33
---

Enrichment is the process by which a record's metadata can be augmented by either fetching information from an external resource or by performing data analysis on a Collection of records.

The DSL allows the harvest operator to specify multiple enrichments for a parser config. Each enrichment can define one or multiple attributes.

__Define a JSON enrichment__

```ruby
enrichment :google_placenames do
  url "http://maps.google.com"
  format "json"

  attribute :locations do
    {
      latitude: path("place/lat"),
      longitude: path("place/lng")
    }
  end
end
```

__Define a XML enrichment__

```ruby
enrichment :ndha_rights do
  requires :tap_id do
    primary[:identifier].first.to_s.gsub("tap:", "")
  end

  url "http://ndhadeliver.govt.nz/?recordId=#{requirements[:tap_id]}"
  format "xml"

  attribute :dc_rights, xpath: "//dc:rights"
end
```

## DSL Enrichment Options

### type
It gives the harvest operator the option to specify an enrichment class to use for a specific enrichment. These are generally special case enrichments. The only cases we currently have at the moment are for tapuhi.
```ruby
enrichment :tapuhi_denormalization, type: "TapuhiDenormalize"
```

The classes we currently support are the following:  

`TapuhiDenormalize`: This class denormalizes the authorities  
`TapuhiRelationships`: This class builds the authority relationships from the relation field on a primary source

### priority
It gives the harvest operator the option to specifiy the priority of an enrichment source.

``` ruby
enrichment :tapuhi_denormalization, priority: -1, type: "TapuhiDenormalize"
```

Negative numbers have a higher priority than positive numbers.  

### required_for_active_record
Setting this to true creates a record in the api that has status: partial until the record has a source with the name of this enrichment. Many enrichments in a parser can have this option. 

``` ruby
enrichment :tapuhi_denormalization, priority: -1, required_for_active_record: true do
...
end
```

*Notes*: 
* Enrichments that contain reject_if block will not write sources for rejected records. If this enrichment is required using this option, rejected records **will not become active**.
* Although technically possible, this is likely to break reflective enrichments (specifying a `type:` option).

## DSL Enrichment Methods

### primary
It gives the harvest operator access to the primary source's attributes through a square bracket notation.

```ruby
primary[:title]

```

It will return an AttributeValue object, which means the operator has access to the same DSL as when working within an attribute definition block.

```ruby
primary[:identifier].find_without(/http/).first
```

### requires
The requires method allows the harvest operator to specify a value that is required in order to be able to perform the enrichment. It also stores the returned value so that it can be conveniently accessed later.

When any of the require blocks returns an empty or nil value, the enrichment will be skiped.

```ruby
requires :tap_id do
  primary[:identifier].first.gsub("tap:", "")
end
```

It then makes the __tap_id__ available through the requirements method.

```ruby
url "http://ndhadeliver.org?recordId=#{requirements[:tap_id]}"
```

### url
It specifies the location of the enrichment resource.

```ruby
url "http://ndhadeliver.govt.nz/?recordId=#{primary[:identifier].first}"
```

### format
It tells the enrichment how it should parse the resource. There are 3 types of resources XML, JSON and File.

XML and JSON resources work the same as in the root of the record and the File resource exposes a number of pre set values about the file.

#### File Resource
The exposed values by the file resource are:
- size
- height
- width
- mime_type
- extension
- url

The following is an example of how to build a thumbnail
```ruby
enrichment :ndha_thumbnail do
  requires :tap_id do
    primary[:thumbnail_url].first.match(/IE[\d]+/)[0]
  end

  url "http://ndhadeliver.natlib.govt.nz/NLNZStreamGate/get?dps_pid=#{requirements[:tap_id]}"
  format "file"
  
  attribute :thumbnail do
    {
      file_size: get(:size).first,
      height: get(:height).first,
      width: get(:width).first,
      file_type: get(:mime_type).first,
      file_extension: get(:extension).first,
      url: get(:url).first
    }
  end
end
```

