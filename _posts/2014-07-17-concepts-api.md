---
layout: page
title: "Concepts API"
category: api_usage
date: 2014-07-17 15:00:53
order: 2
---

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
