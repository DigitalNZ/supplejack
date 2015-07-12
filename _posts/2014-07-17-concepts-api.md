---
layout: page
title: "Concepts API"
category: api_usage
date: 2014-07-17 15:00:53
order: 3
---

### Display context ###
`http://localhost:3000/schema`

### Display a single concept ###
`http://localhost:3000/concepts/1.json?api_key=your-api-key`

#### Request ####

| Parameter        | Description           |
| ------------- |-------------| -----|
| facets        | Facet filtering query. This parameter can be defined more than once |
| fields        | The fields to return |
| inline_context| Displays @context values (defaults to false) |

### Display a records associated with a concept ###
`http://localhost:3000/concepts/1/records.json?api_key=your-api-key`

#### Request ####

| Parameter        | Description           |
| ------------- |-------------| -----|
| text          | The search term(s) |
| facets        | Facet filtering query. This parameter can be defined more than once |
| fields        | The fields to return |
| query_fields  | Fields filtering query |
| sort          | Fields for sorting query |
| direction     | Order of the sort `asc` or `desc` |

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
    "concept_search": {
        "result_count": 1,
        "results": [
            {
                "@context": "http://api.digitalnz.org/schema",
                "@type": "edm:Agent",
                "@id": "http://api.digitalnz.org/concepts/2380",
                "altLabel": [
                    "Walter Auburn",
                    "Walter Salomon (Dr) Auburn"
                ],
                "biographicalInformation": "",
                "concept_id": 2380,
                "dateOfBirth": "1906-06-25",
                "dateOfDeath": "1979-06-25",
                "name": "Walter Salomon (Dr)  Auburn",
                "prefLabel": "Walter Auburn",
                "sameAs": [
                    "http://www.aucklandartgallery.com/the-collection/browse-artists/2804",
                    "http://natlib.govt.nz/records/22391954"
                ]
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=our-api-key&text=Salomon"
    }
}
```

Using `facets`.

`http://localhost:3000/concepts.json?api_key=your-api-key&facets=label`

```json
{
    "concept_search": {
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

`http://localhost:3000/concepts.json?api_key=your-api-key&text=Salomon&fields=prefLabel`

```json
{
    "search": {
        "result_count": 1,
        "results": [
            {
                "@context": "http://api.digitalnz.org/schema",
                "@type": "edm:Agent",
                "@id": "http://api.digitalnz.org/concepts/4043",
                "concept_id": 4043,
                "prefLabel": "Lanyon, Peter"
            }
        ],
        "per_page": 20,
        "page": 1,
        "request_url": "http://localhost:3000/concepts.json?api_key=your-api-key&text=Rita&fields=prefLabel",
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
                "@context": "http://api.digitalnz.org/schema",
                "dateOfBirth": "1866-01-01T12:00:00+00:00",
                "label": "Wilhelm Dittmer"
            },
            {
                "@context": "http://api.digitalnz.org/schema",
                "dateOfBirth": "1869-01-01T12:00:00+00:00",
                "label": "Frances Hodgkins"
            },
            {
                "@context": "http://api.digitalnz.org/schema",
                "dateOfBirth": "1883-01-01T12:00:00+00:00",
                "label": "Salomon van Abbé"
            },
            {
                "@context": "http://api.digitalnz.org/schema",
                "dateOfBirth": "1883-07-31T12:00:00+00:00",
                "label": "Salomon van Abb"
            },
            {
                "@context": "http://api.digitalnz.org/schema",
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
