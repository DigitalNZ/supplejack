---
layout: page
title: "Creating a Schema"
category: api
date: 2014-05-01 16:01:30
order: 1
---

The heart of the Supplejack API is the Schema. The Schema defines the fields for each record and how those fields should be stored and searched. It also defines the different user roles and any restrictions for those roles.

When you first install the Supplejack API a default Schema file is created (see the code at the bottom of the page for a copy). This gives you a template to start working from but you do not have keep any of the fields which are defined in the example. By default the schema file is created at the following location `app/supplejack_api/schema.rb`.

## Schema basics

To start your Schema from scratch you first need the class definition.

```ruby
class Schema < SupplejackApi::SupplejackSchema

end
```

You must name your class `Schema` and it must inherit from `SupplejackApi::SupplejackSchema` so that compulsory fields and groups are included in your Schema.

```ruby
# Supplejack API core fields, these fields are automatically added to your Schema.
:internal_identifier,   # Use to store record identifier. It is expected that you would create this value.
:status,                # Used by Mongoid to mark the record as deleted.
:landing_url,           # A url to view the record.
:created_at,            # When the record was created.
:updated_at             # When the record was last updated.

# You can view the core fields on a record by adding &fields=internal_fields to a request
# e.g. http://localhost:3000/records/1.json?api_key=zNyseQUT9vybmZsBo-af&fields=internal_fields
```

Once you have defined your class you can begin adding fields, groups and roles. For more details about how to do this you can view the [Schema DSL documentation](supplejack/api/schema-dsl-domain-specific-language.html). You must define at least one group and role, and mark them as `default`, before you can view records from your API.

### Some common issues

* If you already have records saved in Mongo you will have to resave them each time you update your Schema before you see any change in what is returned from the API. 
* Fields that are already saved will not be removed from the record, in Mongo, if you remove that field from the Schema.


## Example schema
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
