---
layout: page
title: Architecture
category: supplejack
---
Developed in Ruby on Rails, there are several core components of the Supplejack platform:

* [Manager](/supplejack/start/supplejack-manager.html) (user interface for controlling activity)
* [Worker](/supplejack/start/supplejack-worker.html) (for harvesting, enrichment, and link checking activity)
* [API](/supplejack/start/supplejack-api.html) (public API wrapper to search index and metadata repository)
* [Common](/supplejack/start/supplekack-common.html) (shared helpers for the the Worker and Manager)

Additionally, the Suppejack stack includes:
* [API](/supplejack/start/supplejack-api.html) (public API wrapper to search index and metadata repository)
* [Common](/supplejack/start/supplekack-common.html) (shared helpers for the the Worker and Manager)
* [Website](/supplejack/start/supplejack-website.html) (demo web application that provides simple search, result, filtering, and view interfaces to collected items)
* [Client](/supplejack/start/supplejack-client.html) (client gem used by the demo website to interface with the Supplejack API)

Supplejack relies on integration with both a search index (default is Solr) and a metadata repository (default is MongoDB). 
The code and documentation provided is tailored for a monolithic install, however in a full production environment DigitalNZ runs a cluster of front-end services delivering the public API, supported by a back-end service dedicated to harvesting.

![Supplejack Architecture](images/Master-DigitalNZ-Infrastructure-Supplejack.png) 
