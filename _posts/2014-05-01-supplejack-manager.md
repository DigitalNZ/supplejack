---
layout: page
title: "Supplejack Manager"
category: start
date: 2014-05-01 15:01:36
order: 4
---

The harvester manager is a rails application that provides a visual interface into the harvesting and link checking processes within the harvester worker. The manager uses active resource to interface into the workers models using a restful interface. It allows the harvest operator to do the following:

* Create parsers.
* Run/view harvests.
* Schedule harvests.
* Preview a record before it is harvested.
* Define parser templates.
* Suppress a collection in the Supplejack API.
* View all suppressed collections in the Supplejack API.
* Define rules for checking links in the Supplejack API.
* View the statistics for the link checker

## Writing parser scripts
* [[Parser DSL (Domain Specific Language)|Parser-DSL-(Domain-Specific-Language)]]
* [[Validations|Validations]]
* [[XML-Namespaces|XML-Namespaces]]
* [[Attribute-Transformation-Options|Attribute-Transformation-Options]]
* [[Enrichment|Enrichment]]
* [[Modifiers|Modifiers]]
