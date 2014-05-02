---
layout: page
title: "Creating a Schema"
category: api
date: 2014-05-01 16:01:30
order: 1
---

The heart of the Supplejack API is the Schema. This defines the fields for each record and how those fields should be stored and searched. It also defines the different user roles and any restrictions for those roles.


### Copy of the example schema that is created after install
```ruby 
class Schema < SupplejackApi::SupplejackSchema

  # Fields
  string    :name,         search_boost: 10,      search_as: [:filter, :fulltext]
  string    :address,      search_boost: 2,       search_as: [:filter, :fulltext]
  string    :email,        multi_value: true,     search_as: [:filter]
  string    :children,     multi_value: true
  string    :contact,      multi_value: true
  integer   :age
  datetime  :birth_date
  boolean   :nz_citizen,                          search_as: [:filter]

  # Groups
  group :default do
    fields [
      :name,
      :address
    ]
  end
  group :all do
    includes [:default]
    fields [
      :email,
      :children,
      :nz_citizen,
      :birth_date,
      :age
    ]
  end

   # Roles
  role :developer do
    default true
  end
  role :admin

end
```
