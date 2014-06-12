# Supplejack API Concepts #

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

  # Namespaces
  namespace :skos,   url: 'http://www.w3.org/2004/02/skos/core'
  namespace :foaf,   url: 'http://xmlns.com/foaf/0.1/'
  namespace :rdaGr2, url: 'http://rdvocab.info/ElementsGr2/'
  namespace :edm,    url: 'http://www.europeana.eu/schemas/edm/'
  namespace :owl,    url: 'http://www.w3.org/2002/07/owl'

  # Fields
  string    :concept_id,    store: false
  string    :type
  string    :name,          search_boost: 10,     search_as: [:filter, :fulltext], namespace: :foaf
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
      :name,
      :label,
      :role
    ]
  end
  group :all do
    includes [:default]
    fields [
      :description,
      :dateOfBirth,
      :dateOfDeath,
      :placeOfBirth,
      :placeOfDeath,
      :gender,
      :isRelatedTo,
      :hasMet,
      :sameAs,
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
`http://localhost:3000/concepts.json?api_key=your-api-key&text=David`

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

`http://localhost:3000/concepts.json?api_key=your-api-key&text=david`

```json
{
    "search": {
        "result_count": 2,
        "results": [
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "name": "David Lange",
                "label": "David Lange",
                "role": "politician"
            },
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "name": "David Hill",
                "label": "David Hill",
                "role": "author"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=david",
        "facets": {}
    }
}
```

Using `facets`.

`http://localhost:3000/concepts.json?api_key=your-api-key&facets=name`

```json
{
    "search": {
        "result_count": 5,
        "results": [
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "name": "David Lange",
                "label": "David Lange",
                "role": "politician"
            },
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "name": "Robert Muldoon",
                "label": "Robert Muldoon",
                "role": "politician"
            },
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "name": "Colin McCahon",
                "label": "Colin McCahon",
                "role": "artist"
            },
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "name": "Rita Angus",
                "label": "Rita Angus",
                "role": "artist"
            },
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "name": "David Hill",
                "label": "David Hill",
                "role": "author"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&facets=name",
        "facets": {
            "name": {
                "Colin McCahon": 1,
                "David Hill": 1,
                "David Lange": 1,
                "Rita Angus": 1,
                "Robert Muldoon": 1
            }
        }
    }
}
```

Display specific fields using `fields`.

`http://localhost:3000/concepts.json?api_key=your-api-key&text=rita&fields=name,description`

```json
{
    "search": {
        "result_count": 1,
        "results": [
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "description": "rdaGr2:biographicalInformation"
                },
                "name": "Rita Angus",
                "description": "Rita Angus description"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=rita&fields=name,description",
        "facets": {}
    }
}
```

Search within a specific fields using `query_fields`.

`http://localhost:3000/concepts.json?api_key=your-api-key&text=Rita&text=rita description&query_fields=description`

```json
{
    "search": {
        "result_count": 1,
        "results": [
            {
                "@context": {
                    "foaf": "http://xmlns.com/foaf/0.1/",
                    "name": "foaf:name",
                    "skos": "http://www.w3.org/2004/02/skos/core",
                    "label": "skos:prefLabel",
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "role": "rdaGr2:professionOrOccupation"
                },
                "type": "foaf:person",
                "name": "Rita Angus",
                "label": "Rita Angus",
                "role": "artist"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=rita%20description&query_fields=description",
        "facets": {}
    }
}
```

Sorting search results using `sort` and `direction`.

`http://localhost:3000/concepts.json?api_key=your-api-keysort=dateOfBirth&direction=asc&fields=dateOfBirth`

```json
{
    "search": {
        "result_count": 5,
        "results": [
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth"
                },
                "dateOfBirth": "1600-01-01T00:00:00+00:00"
            },
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth"
                },
                "dateOfBirth": "1700-01-01T00:00:00+00:00"
            },
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth"
                },
                "dateOfBirth": "1915-06-11T05:37:47+00:00"
            },
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth"
                },
                "dateOfBirth": "1950-01-01T00:00:00+00:00"
            },
            {
                "@context": {
                    "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                    "dateOfBirth": "rdaGr2:dateOfBirth"
                },
                "dateOfBirth": "1964-06-11T05:37:11+00:00"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&sort=dateOfBirth&direction=asc&fields=dateOfBirth",
        "facets": {}
    }
}
```
