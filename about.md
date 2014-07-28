---
layout: page
title: About
category: supplejack
---

Supplejack was developed by the DigitalNZ Team at the National Library of New Zealand and Department of Internal Affairs, with the support of local vendors in Wellington. The software came out of an effort by the National Library to aggregate the collections of NZ libraries, archives, museums, broadcasters, communities, government. Supplejack is the 2nd-generation toolset for DigitalNZ. The 1st-generation toolset was built in 2008, with this current stack beginning development in 2012.

### What does Supplejack do?

Its main purpose is to make it easy to aggregate heterogeneous data at scale and provide ways to surface that data so it is more useful. From an information management perspective there are several things you can do with Supplejack:

1. Define common data schema that incoming data should map to
2. Create Solr and MongoDB indexes that conform to your schema
3. Script instructions for extracting and mapping data from many different data sources 
4. Set up validation rules for your data harvesting activity
5. Schedule data harvests to run at whatever frequency you like
6. Run enhancement scripts to improve the quality or completeness of harvested data
7. Deliver a public API of the standardised data
8. Monitoring API key activity and set query throttle rates
8. View collected data on a demo website

Supplejack was designed to provide assurance to the quality of data management activities when working at scale. The largest known user of Supplejack currently uses it to manage the harvesting from more than 200 data sources on a 24/7 basis and supports more than 10 million external API queries a month.

### Features

* Responsive designed web interface for managing harvesting activity
* Support for all types of data sources, including XML, OAI-PMH, RSS, HTML, MARC, RDF, JSON
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
