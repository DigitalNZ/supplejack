---
layout: default
title: "Supplejack"
---

### What is Supplejack?

Supplejack is a platform for managing the harvesting and manipulation of metadata. It was originally developed to manage the sourcing of metadata for the [DigitalNZ](http://www.digitalnz.org/) aggregation service, and has grown to a platform that can manage millions of records from hundreds of data sources.

It's main purpose is to manage the process of acquiring data from remote sources, map data to a standard data schema, manage any quality control or enrichment processes, and surface the cleaned and unified data via a public API.

Key features of the platfrom include:

* Browser-based interface for managing harvesting processes
* 


### Architectural overview

The basic componets of the platform are introduced in this [architecture diagram](https://drive.google.com/file/d/0B63EYVIeMWSfc0R5empaYWxSaHM/edit?usp=sharing) and include:

* Manager user interface
* Workers (for harvesting, enrichment, and link checking activity)
* Integration with search index (default is Solr) 
* Integration with metadata repository (default is MongoDB)
* API (public API wrapper to search index and metadata repository)
