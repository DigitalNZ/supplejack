---
layout: page
title: "Creating Schemas"
category: api
date: 2014-05-01 16:01:30
order: 1
---

The heart of the Supplejack API is the Schema. The Schema defines the fields for each record and how those fields should be stored and searched. It also defines the different user roles and any restrictions for those roles.

When you first install the Supplejack API a default Schema file is created (see the code at the bottom of the page for a copy). This gives you a template to start working from but you do not have keep any of the fields which are defined in the example. By default the schema file is created at the following location `app/supplejack_api/schema.rb`.

## Schema basics

To start your Schema from scratch you first need the class definition.

```ruby
class Schema
  include SupplejackApi::SupplejackSchema

end
```

You must name your class `Schema` and it must include `SupplejackApi::SupplejackSchema` so that compulsory fields and groups are included in your Schema.

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


### Example Record Schema
```ruby 
class RecordSchema
  include SupplejackApi::SupplejackSchema

  # Namespaces
  namespace :dc,        url: 'http://purl.org/dc/elements/1.1/'
  namespace :sj,        url: ''

  # Fields
  string    :record_id,                   store: false,                                           namespace: :sj
  string    :concept_ids,                                                                         namespace: :sj
  string    :title,                       search_boost: 10,     search_as: [:filter, :fulltext],  namespace: :dc
  string    :description,                 search_boost: 2,      search_as: [:filter, :fulltext],  namespace: :dc
  string    :creator,                     multi_value: true,                                      namespace: :dc
  datetime  :date,                                                                                namespace: :dc
  string    :display_date,                                                                        namespace: :dc
  string    :subject,                                                                             namespace: :dc
  string    :identifier,                                                                          namespace: :dc
  string    :rights,                                                                              namespace: :dc
  string    :license,                                                                             namespace: :dc
  string    :type,                        multi_value: true,    search_as: [:filter, :fulltext],  namespace: :dc
  string    :coverage,                                                                            namespace: :dc
  string    :language,                                                                            namespace: :dc
        
  string    :source_provider_name,                              search_as: [:filter, :fulltext],  namespace: :sj
  string    :source_contributor_name,     multi_value: true,    search_as: [:filter, :fulltext],  namespace: :sj
  string    :source_website_name,                               search_as: [:filter, :fulltext],  namespace: :sj
  string    :source_url,                                                                          namespace: :sj
  string    :thumbnail_url

  # Alias thumbnail_url with thumbnail
  string    :thumbnail do
    store false
    search_value do |record|
      record.thumbnail_url
    end
  end

  # Groups
  group :default do
    fields [
      :title,
      :description
    ]
  end

  group :all do
    includes [:default]
    fields [
      :record_id,
      :concept_ids,
      :title,
      :description,
      :creator,
      :date,
      :display_date,
      :subject,
      :identifier,
      :rights,
      :type,
      :coverage,
      :language,
      :source_provider_name,
      :source_contributor_name,
      :source_website_name,
      :source_url,
      :thumbnail_url
    ]
  end

  group :core do
    fields [:record_id]
  end

   # Roles
  role :developer do
    default true
  end
  role :admin

end
```

## Concept Schema ##

To start using Concept, you need to define a new Schema definition called `ConceptSchema`.

Note: You must name you class `ConceptSchema` and it must include `SupplejackApi::SupplejackSchema` so that compulsory fields and groups are included in your Concept schema.

Once you have defined your class you can begin adding fields, namespaces, groups and roles.

### Example Concept Schema ###

```ruby
class ConceptSchema
  include SupplejackApi::SupplejackSchema

  #namespaces
  namespace :skos,   url: 'http://www.w3.org/2004/02/skos/core'
  namespace :foaf,   url: 'http://xmlns.com/foaf/0.1/'
  namespace :rdaGr2, url: 'http://rdvocab.info/ElementsGr2/'
  namespace :edm,    url: 'http://www.europeana.eu/schemas/edm/'
  namespace :owl,    url: 'http://www.w3.org/2002/07/owl'

  # Fields
  string    :concept_id,    store: false
  string    :landing_url,   store: false
  string    :type
  string    :match_status,  search_as: [:filter]
  string    :name,          multi_value: true,    search_boost: 10,     search_as: [:filter, :fulltext], namespace: :foaf
  string    :givenName,     search_boost: 10,     search_as: [:filter, :fulltext], namespace: :foaf
  string    :familyName,    search_boost: 10,     search_as: [:filter, :fulltext], namespace: :foaf
  string    :label,         search_boost: 5,      search_as: [:filter, :fulltext], namespace: :skos, namespace_field: :prefLabel
  string    :description,   search_boost: 2,      search_as: [:filter, :fulltext], namespace: :rdaGr2, namespace_field: :biographicalInformation
  datetime  :dateOfBirth,   search_as: [:filter], namespace: :rdaGr2
  datetime  :dateOfDeath,   search_as: [:filter], namespace: :rdaGr2
  string    :placeOfBirth,  namespace: :rdaGr2  
  string    :placeOfDeath,  namespace: :rdaGr2  
  string    :role,          namespace: :rdaGr2,   namespace_field: :professionOrOccupation
  string    :gender,        search_as: [:filter], namespace: :rdaGr2
  string    :isRelatedTo,   multi_value: true,    namespace: :edm
  string    :hasMet,        multi_value: true,    namespace: :edm
  string    :sameAs,        multi_value: true,    namespace: :owl

  # Groups
  group :default do
    fields [
      :type,
      :label,
      :role
    ]
  end

  group :all do
    includes [:default]
    fields [
      :landing_url,
      :match_status,
      :name,
      :givenName,
      :familyName,
      :description,
      :dateOfBirth,
      :dateOfDeath,
      :placeOfBirth,
      :placeOfDeath,
      :gender,
      :isRelatedTo,
      :hasMet,
      :sameAs
    ]
  end

  group :core do
    fields [:concept_id]
  end

  # Roles
  role :developer do
    default true
  end
  role :admin

end
```