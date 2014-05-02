---
layout: page
title: "Schema DSL (Domain Specific Language)"
category: api
date: 2014-05-02 16:28:49
order: 2
---

## Fields
### Types
Fields can be one of four types: String, Integer, Boolean or Datetime. When defining your fields in your schema you must declare the type of the field first

```ruby
# Comments show example data that would be stored
string   :name         # "Joe Smith"
integer  :age          # 32
datetime :birthdate    # 2006-08-07T00:00:00.000Z
boolean  :employed     # true
```

### Multi value fields
Fields can be defined as multi value. This means that data will be stored as an array of values
```ruby
string :email,  multi_value: true   # e.g. ["joe@test.com", "joe.smith@example.com"]
```
### Search
#### Search boost
Defining a search boost will add a boost to relevancy of the field when searching for records in SOLR. 

```ruby
string :name,    search_boost: 10
string :address, search_boost: 2
```

In the above example a search that matches values in the name field would receive a higher ranking than ones which match the address field.

#### Search as
How the SOLR should search the field. Can either be filter, fulltext or both.  
**Fulltext**:
**Filter**:

```ruby
string :name,     search_as: [:filter, :fulltext]
string :address,  search_as: [:fulltext]
```

#### Search value

#### Store

## Groups
Groups allow you to collect several fields together and reference them using a single value. Groups are used when determining which fields to return if none are specified in the request. This means that you must always define at least the 'default' group.

```ruby
group :default do
  fields [
    :name,
    :address
  ]
end

group :all_fields do
  includes [:default]   # NOTE: Groups can include other groups
  fields [
    :age,
    :gender,
    :email,
    :phone
  ]
end
```

```json
// Sample requests with different groups
// api.supplejack.com/records/123.json?api_key=abc123
{
  "record": {
     name: "Joe Smith",
     address: "123 Smith St, Wellington, New Zealand"
  }
}

// api.supplejack.com/records/123.json?api_key=abc123&fields=all_fields
{
  "record": {
     name: "Joe Smith",
     address: "123 Smith St, Wellington, New Zealand",
     age: 32
     gender: "male"
     email: "joe@test.com"
     phone: "07 123 5457"
  }
}
```

## Roles
You are able to define several roles for users of the API. These can be used to expose different fields 
### Restrictions


