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

### Generate Manager User keys

From the Manager's project root, create a user from the console:

```ruby
rails c
 > User.create!(email: 'your@email.com', name: 'Joe Doe', password: 'p@ssw0rd', password_confirmation: 'p@ssw0rd').
=> #<User _id: 5371508b5311633df8000001, created_at: 2014-05-12 22:51:55 UTC, updated_at: 2014-05-12 22:51:55 UTC, name: "Joe Doe", email: "your@email.com", encrypted_password: "$2a$10$pKS9ydWHRWtbywuIWBBiy.Yn16QR3ZKmuPXFzQIyJqJHZtrb5c1uq", reset_password_token: nil, reset_password_sent_at: nil, remember_created_at: nil, sign_in_count: 0, current_sign_in_at: nil, last_sign_in_at: nil, current_sign_in_ip: nil, last_sign_in_ip: nil, authentication_token: "BYh6ovEAWwLyxJpqRrwE"> 
 > User.last.authentication_token
=> "BYh6ovEAWwLyxJpqRrwE"
```

### Environment Configurations
In order to start harvesting records, you need to specify what environments you need. You can choose `staging` or `production` or both. The example `application.yml` comes with `production` as default environment. If you wanted to have two environments, you can uncomment `staging` and specify the correct values for URLs and keys.

By default, supplejack manager can only create parser to harvest record. In order to create parser for concept harvesting, you need to set ``PARSER_TYPE_ENABLED`` in **application.yml** to true.

Example 1: One environment setup

```yaml
# Example Supplejack Manager application.yml file
# This setup assumes that you have an API that runs on localhost:3000
# and a Worker running on localhost:3002.
# Running the Manager allows you to harvest in production environment.
  
development:
  WORKER_HOST: http://localhost:3002
  WORKER_API_KEY: <production worker key>
  API_HOST: http://localhost:3000
  API_MONGOID_HOSTS: localhost:27017

test:
  WORKER_HOST: http://localhost:3002
  WORKER_API_KEY: <production worker key>
  API_HOST: http://localhost:3000
  API_MONGOID_HOSTS: localhost:27017

production:
  WORKER_HOST: http://localhost:3002
  WORKER_API_KEY: <production worker key>
  API_HOST: http://localhost:3000
  
# staging:
#   WORKER_HOST: http://localhost:4002
#   WORKER_API_KEY: <staging worker key>
#   API_HOST: http://localhost:4000
#   API_MONGOID_HOSTS: localhost:27017
```

Example 2: Two environment setup

```yaml
# Example Supplejack Manager application.yml file
# This setup assumes that you have two APIs that runs on localhost:3000
# and localhost:4000. Workers running on localhost:3002 and localhost:4002.
# Running the Manager allows you to harvest in production and staging environments.
  
development:
  WORKER_HOST: http://localhost:4002
  WORKER_API_KEY: <staging worker key>
  API_HOST: http://localhost:4000
  API_MONGOID_HOSTS: localhost:27017

test:
  WORKER_HOST: http://localhost:4002
  WORKER_API_KEY: <staging worker key>
  API_HOST: http://localhost:4000
  API_MONGOID_HOSTS: localhost:27017

production:
  WORKER_HOST: http://localhost:3002
  WORKER_API_KEY: <production worker key>
  API_HOST: http://localhost:3000
  
staging:
  WORKER_HOST: http://localhost:4002
  WORKER_API_KEY: <staging worker key>
  API_HOST: http://localhost:4000
  API_MONGOID_HOSTS: localhost:27017
```

### Writing parser scripts
* [Parser DSL (Domain Specific Language)](/supplejack/manager/parser-dsl-domain-specific-language.html)
* [Validations](/supplejack/manager/validations.html)
* [XML Namespaces](/supplejack/manager/xml-namespaces.html)
* [Attribute Transformation Options](/supplejack/manager/attribute-transformation-options.html)
* [Enrichments](/supplejack/manager/enrichments.html)
* [Modifiers](/supplejack/manager/modifiers.html)
