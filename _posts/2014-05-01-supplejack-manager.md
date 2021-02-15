---
layout: page
title: "Supplejack Manager"
category: components
date: 2014-05-01 15:01:36
order: 2
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

**Notes:**

* There are two user roles: **admin** and **user**
* Admin user is superuser, requires at least one available
* Normal user is created by default

### Generate Manager User keys

From the Manager's project root, create a user from the console:

```ruby
rails c
 > User.create!(email: 'your@email.com', name: 'Joe Doe', password: 'p@ssw0rd', password_confirmation: 'p@ssw0rd').
=> #<User _id: 5371508b5311633df8000001, created_at: 2014-05-12 22:51:55 UTC, updated_at: 2014-05-12 22:51:55 UTC, name: "Joe Doe", email: "your@email.com", encrypted_password: "$2a$10$pKS9ydWHRWtbywuIWBBiy.Yn16QR3ZKmuPXFzQIyJqJHZtrb5c1uq", reset_password_token: nil, reset_password_sent_at: nil, remember_created_at: nil, sign_in_count: 0, current_sign_in_at: nil, last_sign_in_at: nil, current_sign_in_ip: nil, last_sign_in_ip: nil, authentication_token: "BYh6ovEAWwLyxJpqRrwE">
 > User.last.authentication_token
=> "BYh6ovEAWwLyxJpqRrwE"
```

### Supplejack API integration

Supplejack API harvester endpoints requires an API key with harvester privileges that is configured as enrironment variable. `HARVESTER_API_KEY`.

### Environment Configurations
In order to start harvesting records, you need to specify what environments you need by tagging parser script. You can choose `staging` or `production` or both. The example `application.yml` comes with `staging` as default environment. You can add a new environment eg production by selecting the new key and correct url.

By default, supplejack manager can only create parser to harvest record. In order to create parser for concept harvesting, you need to set ``PARSER_TYPE_ENABLED`` in **application.yml** to true.

Example 1: One environment setup

```yaml
# Example Supplejack Manager application.yml file
# This setup assumes that you have an API that runs on localhost:3000
# and a Worker running on localhost:3002.
# Running the Manager allows you to harvest in production environment.

development:
  WORKER_HOST: http://127.0.0.1:3002
  API_HOST: http://127.0.0.1:3000
  HARVESTER_API_KEY: <YOUR_HARVESTER_KEY>
  API_MONGOID_HOSTS: localhost:27017
  AIRBRAKE_PROJECT_ID: <YOUR_AIRBRAKE_PROJECT_ID>
  AIRBRAKE_PROJECT_API_KEY: <YOUR_AIRBRAKE_PROJECT_API_KEY>
  WORKER_KEY: <YOUR_WORKER_KEY>

test:
  WORKER_HOST: http://127.0.0.1:3002
  API_HOST: http://127.0.0.1:3000
  HARVESTER_API_KEY: <YOUR_HARVESTER_KEY>
  API_MONGOID_HOSTS: localhost:27017
  AIRBRAKE_PROJECT_ID: <YOUR_AIRBRAKE_PROJECT_ID>
  AIRBRAKE_PROJECT_API_KEY: <YOUR_PROJECT_API_KEY>
  WORKER_KEY: <YOUR_WORKER_KEY>

# staging:
#   WORKER_HOST: http://127.0.0.1:3002
#   API_HOST: http://127.0.0.1:3000
#   HARVESTER_API_KEY: <YOUR_HARVESTER_KEY>
#   API_MONGOID_HOSTS: localhost:27017
#   WORKER_KEY: <YOUR_WORKER_KEY>
```

Example 2: Two environment setup

```yaml
# Example Supplejack Manager application.yml file
# This setup assumes that you have two APIs that runs on localhost:3000
# and localhost:4000. Workers running on localhost:3002 and localhost:4002.
# Running the Manager allows you to harvest in production and staging environments.

development:
  WORKER_HOST: http://127.0.0.1:3002
  API_HOST: http://127.0.0.1:3000
  HARVESTER_API_KEY: <YOUR_HARVESTER_KEY>
  API_MONGOID_HOSTS: localhost:27017
  AIRBRAKE_PROJECT_ID: <YOUR_AIRBRAKE_PROJECT_ID>
  AIRBRAKE_PROJECT_API_KEY: <YOUR_AIRBRAKE_PROJECT_API_KEY>
  WORKER_KEY: <YOUR_WORKER_KEY>

test:
  WORKER_HOST: http://127.0.0.1:3002
  API_HOST: http://127.0.0.1:3000
  HARVESTER_API_KEY: <YOUR_HARVESTER_KEY>
  API_MONGOID_HOSTS: localhost:27017
  AIRBRAKE_PROJECT_ID: <YOUR_AIRBRAKE_PROJECT_ID>
  AIRBRAKE_PROJECT_API_KEY: <YOUR_PROJECT_API_KEY>
  WORKER_KEY: <YOUR_WORKER_KEY>

staging:
  WORKER_HOST: http://127.0.0.1:3002
  API_HOST: http://127.0.0.1:3000
  HARVESTER_API_KEY: <YOUR_HARVESTER_KEY>
  API_MONGOID_HOSTS: localhost:27017
  WORKER_KEY: <YOUR_WORKER_KEY>
```

### Writing parser scripts
* [Parser DSL (Domain Specific Language)](/supplejack/manager/parser-dsl-domain-specific-language.html)
* [Validations](/supplejack/manager/validations.html)
* [XML Namespaces](/supplejack/manager/xml-namespaces.html)
* [Attribute Transformation Options](/supplejack/manager/attribute-transformation-options.html)
* [Enrichments](/supplejack/manager/enrichments.html)
* [Modifiers](/supplejack/manager/modifiers.html)

### Enabling MFA on the Manager

MFA can be enabled on the manager by setting the environment variable MFA_ENABLED to true and by adding the environment variable OTP_SECRET_KEY to your application.yaml. This value should be generated with `bundle exec rake secret`. Once you have enabled MFA you can get the secret key through the rails console a give it to a user so that they can set up MFA for themselves. 

```
user = User.where(email: 'bla')
user.otp_secret_key
```

If you are enabling it on an existing application, please run the rake task `users:configure_mfa` to set up the required fields on all of the users. 
