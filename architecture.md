---
layout: page
title: Architecture
category: supplejack
---
Developed in Ruby on Rails, there are three core components of the Supplejack platform:

* [Manager](http://digitalnz.github.io/supplejack/start/supplejack-manager.html) (user interface for controlling activity)
* [Worker](http://digitalnz.github.io/supplejack/start/supplejack-worker.html) (for harvesting, enrichment, and link checking activity)
* [API](http://digitalnz.github.io/supplejack/start/supplejack-api.html) (public API wrapper to search index and metadata repository)

Supplejack relies on integration with both a search index (default is Solr) and a metadata repository (default is MongoDB).

![Supplejack Architecture](images/Master-DigitalNZ-Infrastructure-Supplejack.png) 