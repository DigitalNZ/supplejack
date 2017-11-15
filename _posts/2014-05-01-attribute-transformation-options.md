---
layout: page
title: "Attribute Transformation Options"
category: manager
date: 2014-05-01 16:11:48
order: 5
---

Transformation options are changes made to the harvested values, global transformations are applied to every value and custom transformations are only applied when specified in the options for a attribute definition.

## Global Transformations

### Strip whitespace

By default leading and trailing whitespace will be removed from every attribute value.

### Strip HTML

By default every HTML tag will be removed from every attribute value

## Custom Transformations

### Truncation

It will truncate the description to 300 characters

```ruby
attribute :description, xpath: "//dc:description", truncate: 300
```

By default it will add triple dots at the end of the truncated value. To change from three dots to something else do the following:

```ruby
attribute :description, xpath: "//dc:description", truncate: {length: 300, omission: "....."}
```

The omission value is taken into account for the total size of the truncated value so that it will always be the specified size.

### Date
#### Generic date parser
It will try to intelligently understand and parse the date

```ruby
attribute :date, xpath: "//dc:date", date: true
```

#### Template date parser
When the date is in a known specific format and is not understood by the generic date parser you can provide a template that will be used to interpret the date

```ruby
attribute :date, xpath: "//dc:date", date: "%d/%m/%Y"
```

See [Ruby strftime documentation](http://ruby-doc.org/stdlib-2.3.0/libdoc/date/rdoc/DateTime.html#method-i-strftime) for more information.

### Join Values

It will join a array of values into a single string delimited by the character specified

```ruby
attribute :tag, xpath: "//tags", join: ", "
```

### Split values

It will split a string into a array of values based on the delimiter specified.

```ruby
attribute :tag, xpath: "//tags", separator: ", "
```

### Mapping Values

There are certain cases when values need to be mapped to some other values, the most common case in the parser configurations are the rights/licence where the type of license is extracted from a URL.

```ruby
attribute :license, xpath: "//tags", mappings: {
                            ".*Attribution$" => "CC-BY",
                            ".*Attribution_-_Share_Alike$" => "CC-BY-SA",
                            ".*Attribution_-_Non-Commercial_-_No_Derivatives$" => "CC-BY-NC-ND"
                          }
```
