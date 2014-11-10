---
layout: page
title: About
category: supplejack
---

### History

Supplejack was developed by the DigitalNZ Team at the National Library of New Zealand and Department of Internal Affairs, with the support of local vendors in Wellington. The software came out of an effort by the National Library to aggregate the collections of NZ libraries, archives, museums, broadcasters, communities and government so as to better support the use of NZ digital material. DigitalNZ's first generation harvesting tools were built in 2008. After several years of rapid growth, it became clear that a new approach for working at scale was required. Supplejack is DigitalNZ's second generation toolset. It became an active production service in 2013.

### What Supplejack does

It's main purpose is to make it easy to aggregate heterogeneous data at scale and provide ways to surface that data so it is more useful. From a data management perspective there are several things you can do with Supplejack:

1. Define common data schema that incoming data should map to
2. Create search and database indexes that conform to your chosen schema
3. Script instructions for extracting and mapping data from many different data sources 
4. Set up validation rules for your data harvesting activity
5. Schedule data harvests to run at whatever frequency you like
6. Run enhancement scripts to improve the quality or completeness of harvested data
7. Deliver a public API of the standardised data
8. Monitor API key activity and set query throttle rates
8. View collected data on a demo website

Supplejack was designed to provide assurance to the quality of data management activities when working at scale. The largest known user of Supplejack currently uses it to manage the harvesting from more than 200 data sources on a 24/7 basis and supports more than 10 million external API queries a month.

### Features

* Responsive designed web interface for managing harvesting activity
* Support for a wide range of common data sources, including XML, OAI-PMH, RSS, HTML, MARC, RDF, JSON
* Customisable [data schema](http://digitalnz.github.io/supplejack/api/creating-a-schema.html)
* A rich [Domain Specific Language](http://digitalnz.github.io/supplejack/manager/introduction-to-parser-scripts.html) for writing the harvest instructions for each data source
* History of all edits to parser scripts
* Ability to test and preview all parser scripts
* Ability to embed ruby code into parser scripts for complex data harvests or enrichments
* Inbuilt error-checking, and notification routines during the harvesting and enrichment processes
* Code snippets that can be used to insert parser scripting blocks for reuse across different harvests
* Logs harvesting activity to a dashboard for management support
* Support for [manipulation](http://digitalnz.github.io/supplejack/manager/modifiers.html), [validation](http://digitalnz.github.io/supplejack/manager/validations.html), [namespacing](http://digitalnz.github.io/supplejack/manager/xml-namespaces.html), [transformation](http://digitalnz.github.io/supplejack/manager/attribute-transformation-options.html), and [enrichment](http://digitalnz.github.io/supplejack/manager/enrichments.html) of data
* Scheduling options for regular reharvests and incremental updates and deletions. Can be set to any increment such as every hour, day, month etc. at a specific time
* Throttling and response time control to specifiy rate at which individual harvests run
* Broken link monitoring with automatic search index suppression and restoration routines
* Manual control for the suppression of records or collections from the search index
* API framework for the sharing of stadardised data
* API dashboard for monitoring API key activity and setting query throttle rates
* Environment configuration for staging and prodcution environments
* Client gem for support the build of ruby applications against the API
* Demo website application for out-of-the-box display of standardised data

### Introducing fragments

Fragments are a fundamental part of the Supplejack data model that support a level of data provenance. A separate data fragment is created each time a new data source updates a RECORD or CONCEPT (see below for an explanation of the difference). This enables enrichment scripts to run somewhat independently from an original harvest. For example if a RECORD is created from Source 1, and then a particular field is updated from Source 2 (such as when a geo-coding service might provide lat-long co-ordinates) then the data from different sources is stored in separate fragments. This means that a reharvest from a source will not overwrite the other source updates. The Supplejack API retrieves data from an additional merged fragment that brings together all the data from respective fragments.


### Introducing records and concepts

*WARNING: Here be dragons. The CONCEPT feature is still in experimental mode and does not yet work reliably. We would welcome comments and suggestions for improvement.*

The standard use case for Supplejack is in collecting metadata RECORDS about images, documents, books, publications, audio, video, and other types of items (although there is no reason why Supplejack couldn't be used for any kind of data service). In the [creating schema](/supplejack/api/creating-schemas.html) documentation you will find an example schema for setting up such a service for aggregating RECORDS. 

In addition to supporting the collection of RECORDS, Supplejack also includes a prototype for the aggregation of CONCEPTS. In the world of library science, CONCEPTS are more commonly referred to as [Authorities](http://en.wikipedia.org/wiki/Authority_control). Supplejack uses the idea of CONCEPTS to build out a new feature for the crosswalking of authorities, and in our case we are starting with people. In the [creating schema](/supplejack/api/creating-schemas.html) documentation you will also find an example schema for a people CONCEPT. If you wanted to harvest people authorities from different sources (such as VIAF, Library of Congress, or Wikipedia) and create a crosswalking service where all information could be queried, you can use the CONCEPT prototype to:

* harvest multiple CONCEPT (authority) sources
* find the matching CONCEPTS across different sources (e.g. find the people who appear in multiple authority files)
* record all matching CONCEPTS  in a sameAs field
* decide whether field data from a matching CONCEPT should add to or overwrite existing field data (e.g. add a place of death if that data is not already stored in a specific person CONCEPT)
* link item RECORDS to a stored CONCEPT (e.g. link a photo to a photographers person CONCEPT)
* use the Supplejack API to query all the matching CONCEPTs to find possible matches (e.g. search for a particular person and see what people's names match)

For details on setting up CONCEPTS, see the following documentation on [creating schema](/supplejack/api/creating-schemas.html), [CONCEPT configuration](/supplejack/manager/concept-configuration.html), [CONCEPT matching](/supplejack/manager/parser-dsl-domain-specific-language.html), [CONCEPT API](/supplejack/api_usage/concepts-api.html)

