---
layout: page
title: "Code Snippets"
category: manager
date: 2014-05-21 11:53:13
---

Code snippets allow the harvest operator to create pieces of code that can be used across different parsers.

## Default Values

One of the uses for snippets can be to define default values. For example we could define a @@truncate_length variable that holds the default value for the number of characters that a description might have.

To do this create a snippet.

__In snippet "Default Values"__

```ruby
@@truncate_length = 10
```

__In parser definition__

```ruby
class TestSource < HarvesterCore::Xml::Base
  
  include_snippet "Default Values"
  
  attribute :description, xpath: "//description", truncate: @@truncate_length
end
```

## Block definitions

A block definition that would need to be reused across parsers or fields can be reused.

To create the block definition do the following:

__In a snippet "License Definition"__
```ruby
@@license_definition = Proc.new do
  fetch("//license").mapping.... # Write whatever you would write in any block definition.
end
```

__In parser definition__

```ruby
class TestSource < HarvesterCore::Xml::Base
  
  include_snippet "License Definition"
  
  attribute :license, &@@license_definition
end
```

__The above example would be the exact equivalent of doing the following:__

```ruby
class TestSource < HarvesterCore::Xml::Base
    
  attribute :license do
    fetch("//license").mapping.... # Write whatever you would write in any block definition.
  end
end
```

## Mappings

A hash can be defined as a mapping that can latter be reused in any parser.

__In a snippet "License Mappings"__
```ruby
@@license_mapping = {
  ".*Attribution$" => "CC-BY",
  ".*Attribution_-_Share_Alike$" => "CC-BY-SA",
  ".*Attribution_-_No_Derivatives$" => "CC-BY-ND",
  ".*Attribution_-_Non-commercial$" => "CC-BY-NC",
  ".*Attribution_-_Non-commercial_-_Share_Alike$" => "CC-BY-NC-SA",
  ".*Attribution_-_Non-Commercial_-_No_Derivatives$" => "CC-BY-NC-ND"
}
end
```

__In parser definition__

```ruby
class TestSource < HarvesterCore::Xml::Base
  
  include_snippet "License Mappings"
  
  attribute :license, xpath: "//license", mapping: @@license_mapping
end
```

## Multiple attribute definitions

You can even define any number of attributes or directives that you would use in any particular parser and include them.

__In a snippet "OAI default fields"__
```ruby
attribute :date,              from: "dc:date", date: true
attribute :display_date,      from: "dc:date", date: "%d/%m/%Y"
attribute :format,            from: "dc:format"
attribute :title,             from: "dc:title"
attribute :coverage,          from: "dc:coverage"
```

```ruby
class SomeOAI < HarvesterCore::Oai::Base
  include_snippet "OAI default fields"
end
```

__The code above would be the equivalent of doing this:__

```ruby
class SomeOAI < HarvesterCore::Oai::Base
  attribute :date,                from: "dc:date", date: true
  attribute :display_date,        from: "dc:date", date: "%d/%m/%Y"
  attribute :format,              from: "dc:format"
  attribute :title,               from: "dc:title"
  attribute :coverage,            from: "dc:coverage"
end
```

