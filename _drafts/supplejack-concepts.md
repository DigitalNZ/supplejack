# Supplejack API Concepts #

This documention provides implementation details for a new DigitalNZ concepts API. It is expected that the API will handle several different types of concept, in the first instance people and places.

## Concept Schema ##

To start using Concept, you need to define a new Schema definition called `ConceptSchema`.

```ruby
class ConceptSchema < SupplejackApi::SupplejackSchema

end
```

Note: You must name you class `ConceptSchema` and it must inherit from `SupplejackApi::SupplejackSchema` so that compulsory fields and groups are included in your Concept schema.

Once you have defined your class you can begin adding fields, groups and roles.

### Example Concept Schema ###

```ruby
class ConceptSchema < SupplejackApi::SupplejackSchema

  # Fields
  string    :type,                              search_as: [:filter]
  string    :name,          search_boost: 10,   search_as: [:filter, :fulltext]
  string    :label,         search_boost: 5,    search_as: [:filter, :fulltext]
  string    :description,   search_boost: 2,    search_as: [:filter, :fulltext]
  datetime  :dateOfBirth,                       search_as: [:filter]
  datetime  :dateOfDeath,                       search_as: [:filter]
  string    :placeOfBirth
  string    :placeOfDeath
  string    :gender,                            search_as: [:filter]
  string    :isRelatedTo,   multi_value: true
  string    :hasMet,        multi_value: true
  string    :sameAs,        multi_value: true
  string    :role

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
                "@context": {...},
                "@id": "http://digitalnz.org/person/rita-angus",
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
                "@context": {...},
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
                "@context": {...},
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
