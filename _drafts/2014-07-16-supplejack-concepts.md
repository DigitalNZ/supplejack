---
layout: page
title: "Concepts"
category: api
date: 2014-05-01 16:01:30
order: 2
---

This documention provides implementation details for a new DigitalNZ concepts API. It is expected that the API will handle several different types of concept, in the first instance people and places.

## Concept Schema ##

To start using Concept, you need to define a new Schema definition called `ConceptSchema`.

```ruby
class ConceptSchema < SupplejackApi::SupplejackSchema

end
```

Note: You must name you class `ConceptSchema` and it must inherit from `SupplejackApi::SupplejackSchema` so that compulsory fields and groups are included in your Concept schema.

Once you have defined your class you can begin adding fields, namespaces, groups and roles.

### Example Concept Schema ###

```ruby
class ConceptSchema < SupplejackApi::SupplejackSchema

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
  string    :name,          search_boost: 10,     search_as: [:filter, :fulltext], namespace: :foaf
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

## Search ##

### Search for concepts ###
`http://localhost:3000/concepts.json?api_key=your-api-key&text=Salomon`

#### Request ####

| Parameter        | Description           |
| ------------- |-------------| -----|
| text          | The search term(s) |
| facets        | Facet filtering query. This parameter can be defined more than once |
| fields        | The fields to return |
| query_fields  | Fields filtering query |
| sort          | Fields for sorting query |
| direction     | Order of the sort `asc` or `desc` |

#### Examples ####

Search using `text`.

`http://localhost:3000/concepts.json?api_key=your-api-key&text=Salomon`

```json
{
    "search": {
        "result_count": 2,
        "results": [
            {
                "@context": {
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "label": "Salomon van Abb",
                "role": null
            },
            {
                "@context": {
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "[\"foaf:person\"]",
                "label": "Salomon van Abbé",
                "role": null
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=Salomon",
        "facets": {}
    }
}
```

Using `facets`.

`http://localhost:3000/concepts.json?api_key=your-api-key&facets=label`

```json
{
    "search": {
        "result_count": 5,
        "results": [
            .
            .
            .
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&facets=label",
        "facets": {
            "label": {
                "Frances Hodgkins": 1,
                "Rita Angus": 1,
                "Salomon van Abb": 1,
                "Salomon van Abbé": 1,
                "Wilhelm Dittmer": 1
            }
        }
    }
}
```

Display specific fields using `fields`.

`http://localhost:3000/concepts.json?api_key=your-api-key&text=Salomon&fields=label,familyName,givenName`

```json
{
    "search": {
        "result_count": 1,
        "results": [
            {
                "@context": {
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "givenName": "foaf:givenName",
                    "familyName": "foaf:familyName"
                },
                "label": "Rita Angus",
                "familyName": "Angus",
                "givenName": "Rita"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=Rita&fields=label,familyName,givenName",
        "facets": {}
    }
}
```

Search within a specific fields using `query_fields`.

`http://localhost:3000/concepts.json?api_key=your-api-key&text=Angus&query_fields=familyName`

```json
{
    "search": {
        "result_count": 1,
        "results": [
            {
                "@context": {
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "[\"foaf:person\"]",
                "label": "Rita Angus",
                "role": null
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=Angus&query_fields=familyName",
        "facets": {}
    }
}
```

Sorting search results using `sort` and `direction`.

`http://localhost:3000/concepts.json?api_key=your-api-key&sort=dateOfBirth&direction=asc&fields=dateOfBirth,label`

```json
{
    "search": {
        "result_count": 5,
        "results": [
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel"
                },
                "dateOfBirth": "1866-01-01T12:00:00+00:00",
                "label": "Wilhelm Dittmer"
            },
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel"
                },
                "dateOfBirth": "1869-01-01T12:00:00+00:00",
                "label": "Frances Hodgkins"
            },
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel"
                },
                "dateOfBirth": "1883-01-01T12:00:00+00:00",
                "label": "Salomon van Abbé"
            },
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel"
                },
                "dateOfBirth": "1883-07-31T12:00:00+00:00",
                "label": "Salomon van Abb"
            },
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel"
                },
                "dateOfBirth": "1908-03-12T12:00:00+00:00",
                "label": "Rita Angus"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&sort=dateOfBirth&direction=asc&fields=dateOfBirth,label",
        "facets": {}
    }
}
```
