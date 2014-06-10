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

  #namespaces
  namespace :skos,   url: 'http://www.w3.org/2004/02/skos/core'
  namespace :foaf,   url: 'http://xmlns.com/foaf/0.1/'
  namespace :rdaGr2, url: 'http://rdvocab.info/ElementsGr2/'
  namespace :edm,    url: 'http://www.europeana.eu/schemas/edm/'
  namespace :owl,    url: 'http://www.w3.org/2002/07/owl'

  # Fields
  string    :@type
  string    :name,          search_boost: 10,   search_as: [:filter, :fulltext], namespace: :foaf
  string    :label,         search_boost: 5,    search_as: [:filter, :fulltext], namespace: :skos, namespace_field: :prefLabel
  string    :description,   search_boost: 2,    search_as: [:filter, :fulltext], namespace: :rdaGr2, namespace_field: :biographicalInformation
  datetime  :dateOfBirth,   namespace: :rdaGr2
  datetime  :dateOfDeath,   namespace: :rdaGr2
  string    :placeOfBirth,  namespace: :rdaGr2
  string    :placeOfDeath,  namespace: :rdaGr2
  string    :role,          namespace: :rdaGr2, namespace_field: :professionOrOccupation
  string    :gender,        namespace: :rdaGr2
  string    :isRelatedTo,   multi_value: true, namespace: :edm
  string    :hasMet,        multi_value: true, namespace: :edm
  string    :sameAs,        multi_value: true, namespace: :owl

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
end
```

## Search ##

### Search for concepts ###
`http://localhost:3000/concepts.json?api_key=your-api-key&text=David`

#### Request ####

| Parameter        | Description           |
| ------------- |-------------| -----|
| text          | The search term(s) |
| facets        | Facet filtering query. This parameter can be defined more than once. |
| fields        | The fields to return. |
| query_fields  | Fields filtering query. |

#### Examples ####

Using `text`.

`http://localhost:3000/concepts.json?api_key=your-api-key&text=Rita`

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
                  "label": "skos:prefLabel"
                },
                "type": "person",
                "name": "Angus",
                "label": "Rita"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=Rita",
        "facets": {}
    }
}

```

Using `facets`.

`http://localhost:3000/concepts.json?api_key=your-api-key&facets=name`

```json
{
    "search": {
        "result_count": 3,
        "results": [...],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&facets=name",
        "facets": {
            "name": {
                "Angus": 1,
                "Colin McCahon": 1,
                "David Lange": 1
            }
        }
    }
}
```

Display specific fields using `fields`.

`http://localhost:3000/concepts.json?api_key=your-api-key&text=Rita&fields=type,label`

```json
{
    "search": {
        "result_count": 1,
        "results": [
            {
                "@context": {
                  "skos": "http://www.w3.org/2004/02/skos/core",
                  "label": "skos:prefLabel"
                },
                "type": "person",
                "label": "Rita"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=Rita&fields=type",
        "facets": {}
    }
}
```

Search within a specific fields using `query_fields`.

`http://localhost:3000/concepts.json?api_key=your-api-key&text=Rita&text=Short Rita Angus description&query_fields=description&fields=description`

```json
{
    "search": {
        "result_count": 1,
        "results": [
            {
                "@context": {
                  "rdaGr2": "http://rdvocab.info/ElementsGr2/",
                  "description": "rdaGr2:biographicalInformation"
                },
                "description": "Short Rita Angus description"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=Short%20Rita%20Angus%20description&query_fields=description&fields=description",
        "facets": {}
    }
}
```
