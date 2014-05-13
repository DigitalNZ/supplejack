---
layout: default
title: "Supplejack"
---

### What is Supplejack?

Supplejack is a platform for managing the harvesting and manipulation of metadata. It was originally developed to manage the sourcing of metadata for the [DigitalNZ](http://www.digitalnz.org/) aggregation service, and has grown to a platform that can manage millions of records from hundreds of data sources.

It's main purpose is to manage the process of acquiring data from remote sources, map data to a standard data schema, manage any quality control or enrichment processes, and surface the cleaned and unified data via a public API.

<iframe width="420" height="315" src="//www.youtube.com/embed/MLUURxcfcLc" frameborder="0" allowfullscreen></iframe>

Key features of the platfrom include:

* Browser-based interface for managing harvesting processes
* Scheduling features for regular reharvests and incremental updates and deletions. Can be set to any increment such as every hour, day, month etc. at a specific time
* Throttling to control the rate at which we reharvest from individual data sources
* Broken link monitoring with automatic search index suppression and restoration routines
* Manual control for the suppression of records or collections from the search index… to support take-down requests and partner outages
* A very rich Domain Specific Language for scripting the harvest instructions. The functions we have built into the parsing language cover support for all types of data issues we have encountered over five years of harvesting. These functions are fully documented
* Customisable code "snippets" that can be used to insert scripting blocks for reuse across different harvests
* Enrichment support that allows a single record to be created from multiple data sources. For example, we can grab the location of thumbnails from a webpage if it's not listed in an RSS feed, call a geocoding service to add lat-long co-ordinates to a record, or request a full graph of a collection hierarchy so as to calculate child counts on the fly. Enrichment support is designed with data provenance in mind so that, for a record with multiple data sources, we can update the field data from one particular source without over-writing all the other field data from other sources
* Support for authority data. For example, we ingest library and archive authority files into our DigitalNZ search indexes. The system has been designed so that, when coupled with the enrichment model, we can create linked data structures from different authority sources. Harvesting and linking NZ authority sources is part of our planned work over the next several years and there are more ways we can enhance Supplejack to support LD
* Inbuilt validation, error-checking, and notification routines during the harvesting and enrichment processes
* Ability to work at scale. We are currently harvesting from more than 200 data sources, with more than 100 reoccurring reharvests that happen automatically. We don't really know what the effective limit would be, but we believe the interface and architecture can support scale into the thousands of data sources… I think we have to assume some refactoring over time though
* As a bonus… the responsive web design allows the harvests to be managed from the field. Have used it on an iPad from time-to-time, and even had to administer from a phone on one occasion. Not the most efficient form factor, but good to know it works!



### Architectural overview

The basic componets of the platform are introduced below ![Supplejack Architecture](images/Master-DigitalNZ-Infrastructure-Supplejack-View.png) 

The Supplejack stack includes:

* Manager user interface
* Workers (for harvesting, enrichment, and link checking activity)
* Integration with search index (default is Solr) 
* Integration with metadata repository (default is MongoDB)
* API (public API wrapper to search index and metadata repository)
