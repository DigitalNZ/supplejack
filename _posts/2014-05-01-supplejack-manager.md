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

### Installation

1. Clone the manager from GitHub.
`git clone https://github.com/DigitalNZ/supplejack_manager.git`

2. Modify `config/application.yml` file.
  - `WORKER_HOST` - Host URL of the worker 
`e.g. http://localhost:4002`
  - `WORKER_API_KEY` - An authentication key in order to authorize incoming requests from Manager.
  - `API_HOST` - Host URL of your API 
 `e.g. http://localhost:4000`
  - `API_MONGOID_HOSTS`: Host URL of your MongoDB. Defaults to `localhost:27017`

3. [Install the Worker](http://digitalnz.github.io/supplejack/start/supplejack-worker.html).

### Generate Manager User keys

1. From the Manager's project root, Run `rails c`.
2. Create your first user `User.create!(email: 'your@email.com', name: 'Joe Doe', password: 'p@ssw0rd', password_confirmation: 'p@ssw0rd')`.
3. Copy the generated `authentication_token`.
4. Paste it inside your Worker's `application.yml` as `MANAGER_API_KEY`.

### Writing parser scripts
* [[Parser DSL (Domain Specific Language)|Parser-DSL-(Domain-Specific-Language)]]
* [[Validations|Validations]]
* [[XML-Namespaces|XML-Namespaces]]
* [[Attribute-Transformation-Options|Attribute-Transformation-Options]]
* [[Enrichment|Enrichment]]
* [[Modifiers|Modifiers]]
