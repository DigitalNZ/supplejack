---
layout: page
title: "Creating Schemas"
category: api
date: 2014-05-01 16:01:30
order: 1
---

The heart of the Supplejack API is the Schema. The Schema defines the fields for each record and how those fields should be stored and searched. It also defines the different user roles and any restrictions for those roles.

When you first install the Supplejack API a default Schema file is created (see the code at the bottom of the page for a copy). This gives you a template to start working from but you do not have keep any of the fields which are defined in the example. By default the schema file is created at the following location `app/supplejack_api/schema.rb`.

**It is important that you have set up schema correctly as other components eg Supplejack Manager is dependent on it** 

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

### Field definition

Field definition is important. The format is `[field_type] [field_name], [options]`

For example, if you want a field called `title` of type `String`
```ruby
   string        :title,          search_boost: 1, search_as: [:filter, :fulltext]
[field_type]  [field_name]        [options]
```

##### Keywords :book:
* `search_as`: Define search behavior, `:filter` means the field is facetable, `:fulltext` means searchable
* `store`: Make the field writable to DB, default is `true`
* `search_boost`: Define Solr search priority

There are for 4 field types: `string`, `integer`, `datetime`, `boolean`. You can refer to sample record schema as example.

### Aliasing and Solr name
If you need to create an alias or a virtual field, you can do that using the `search_value`.

```ruby
  # Alias thumbnail_url with thumbnail
  string    :thumbnail do
    store false
    search_value do |record|
      record.thumbnail_url
    end
  end
```

You can also change the name of the Solr field using `solr_name`.

```ruby
  # Store "name" in Mongo but search as "full_name" in Solr.
  string    :name do
    search_as [:full_text]
    solr_name :full_name
  end

  # Or using shorthand version
  string    :name,    search_as: [:full_text],    solr_name: :full_name
```

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
  string    :source_type,                                                                         default_value: "Unknown"
  string    :display_date,                                                                        date_format: "%y/%d/%m"  
  string    :thumbnail_url

  # Alias thumbnail_url with thumbnail
  string    :thumbnail do
    store false
    search_value do |record|
      record.thumbnail_url
    end
  end

  # Geospatial features require a field defined with latlon. A record should have fields named lat and lng
  latlon(:lat_lng) do
    search_as [:filter]
    search_value do |record|
      Sunspot::Util::Coordinates.new(record.lat, record.lng)
    end
  end  

  # Change the Solr name
  string    :name,                        multi_value: true,    search_as: [:filter, :fulltext],  namespace: :sj, solr_name: :full_name

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

  # Mongo indexes
  mongo_index :title, fields: [{ title: 1 }]
  mongo_index :description, fields: [{ description: 1 }]

  # Record fields
  model_field :status, field_options: { type: Boolean }, index_fields: { status: 1 }, index_options: { background: true }, validation: { presence: true }

  # or using block
  model_field :state do
    field_options { type: Boolean }
    index_fields { state: 1 }
    index_options { background: true }
    validation { presence: true }
  end
end
```

## Concept Schema ##

To start using Concept, you need to define a new Schema definition called `ConceptSchema`.

Note: You must name you class `ConceptSchema` and it must include `SupplejackApi::SupplejackSchema` so that compulsory fields and groups are included in your Concept schema.

Once you have defined your class you can begin adding fields, namespaces, groups and roles.

### Example Concept Schema ###

```ruby
class ConceptSchema
  include SupplejackApi::SchemaDefinition

  # Namespaces
  namespace :dcterms,     url: 'http://purl.org/dc/terms/'
  namespace :edm,         url: 'http://www.europeana.eu/schemas/edm/'
  namespace :foaf,        url: 'http://xmlns.com/foaf/0.1/'
  namespace :owl,         url: 'http://www.w3.org/2002/07/owl#'
  namespace :rdaGr2,      url: 'http://rdvocab.info/ElementsGr2/'
  namespace :rdf,         url: 'http://www.w3.org/1999/02/22-rdf-syntax-ns#'
  namespace :rdfs,        url: 'http://www.w3.org/2000/01/rdf-schema#'
  namespace :skos,        url: 'http://www.w3.org/2004/02/skos/core#'
  namespace :xsd,         url: 'http://www.w3.org/2001/XMLSchema#'
  namespace :dc,          url: 'http://purl.org/dc/elements/1.1/'

  # Fields (SourceAuthority fields)
  string      :name
  string      :prefLabel
  string      :altLabel,                  multi_value: true
  datetime    :dateOfBirth
  datetime    :dateOfDeath
  string      :biographicalInformation
  string      :sameAs,                    multi_value: true
  string      :givenName
  string      :familyName
  integer     :birthYear
  integer     :deathYear

  group :source_authorities
  group :reverse
  
  model_field :name, field_options: { type: String }, search_as: [:fulltext], search_boost: 6, namespace: :foaf
  model_field :prefLabel, field_options: { type: String }, namespace: :skos
  model_field :altLabel, field_options: { type: Array }, search_as: [:fulltext], search_boost: 2, namespace: :skos
  model_field :dateOfBirth, field_options: { type: Date }, namespace: :rdaGr2
  model_field :dateOfDeath, field_options: { type: Date }, namespace: :rdaGr2
  model_field :biographicalInformation, field_options: { type: String }, search_as: [:fulltext], search_boost: 1,  namespace: :rdaGr2
  model_field :sameAs, field_options: { type: Array }, namespace: :owl
  
  # Use store: false to display the fields in the /schema
  model_field :title, store: false, namespace: :dc
  model_field :date, store: false, namespace: :dc
  model_field :description, store: false, namespace: :dc
  model_field :agents, store: false, namespace: :edm
end
```
