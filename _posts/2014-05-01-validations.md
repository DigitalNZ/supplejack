---
layout: page
title: "Validations"
category: manager
date: 2014-05-01 16:12:17
order: 3
---

Validations allow the harvest operator to specify a minimum set of requirements on the quality of the metadata of a specific record that has to be fullfilled in order for the record to be sent to the API.

## Presence

The presence validation checks that a specific attribute has some value. A very typical example would be to check for the presence of title.

```ruby
class ParserXml < HarvesterCore::Xml::Base
  validates :title, presence: true
  attribute :title, xpath: "//title"
end
```

## Format

The format validation checks that all values within a attribute conform to a specific regular expression. A common exmaple would be to check for the format of a URL (landing_url, thumbnail_url, etc..).

```ruby
class ParserXml < HarvesterCore::Xml::Base
  validates :landing_url, format: {with: /https?:\/\/[\S]+/}
  attribute :landing_url, xpath: "//url"
end
```

## Inclusion

The inclusion validator checks that all values within a attribute are part of a fixed set of specified values.

```ruby
class ParserXml < HarvesterCore::Xml::Base
  validates :dc_type, inclusion: {in: ["Images", "Videos", "News"]}
  attribute :dc_type, xpath: "//dc-type"
end
```

In the previous example a record will be invalid if the dc_type is not any of the three values specified.

## Exclusion

It works the same as the inclusion validator but in reverse.

```ruby
class ParserXml < HarvesterCore::Xml::Base
  validates :dc_type, exclusion: {in: ["Manuscripts"]}
  attribute :dc_type, xpath: "//dc-type"
end
```

In the previous case the record will be invalid if the dc_type is Manuscripts.

## Size

The size validator checks that a attribute has a specific number of values. This can be used for the landing_url for exmaple where you only want 1 value.

```ruby
class ParserXml < HarvesterCore::Xml::Base
  validates :landing_url, size: { is: 1 } # It has to be exactly 1 to be valid
  attribute :landing_url, xpath: "//url"
  
  # Other options allowed are:
  
  validates :landing_url, size: { maximum: 3 } # With 3 or less values the record is valid
  validates :landing_url, size: { minimum: 2 } # With 2 or more values the record is valid
  
  # Also a range is allowed:
  
  validates :landing_url, size: { within: 1..5 } # With any number of values between 1 and 5 it will be valid
end
```

