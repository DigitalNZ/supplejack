---
layout: page
title: "Install & Setup"
category: start
date: 2014-05-01 15:02:23
order: 2
---

Once you have installed all the [dependancies](supplejack/start/dependancies.html) you are now ready to install the rest of the stack. As all the components require configuration to interact we strongly recommend that you use the Rails Application templates that are provided for Supplejack.

## Install the entire stack

This script creates a new app using the Supplejack API and then installs and configures the Supplejack Manager and Supplejack Worker applications.

Once complete you will be able to access your API and harvest new data from exteranl sources. 

To install the full stack run the following command  

#### This install template is NOT complete and will be available soon...

```bash
rails _3.2.12_ new mysupplejack_api_name -m https://raw.github.com/digitalnz/supplejack_template/master/supplejack_template.rb
```

Once this is complete you do not need to run the commands below as all three components will be installed

## Install separate components

### Installing the API

To create a new app using the Supplejack API engine run the following command

```bash
rails _3.2.12_ new mysupplejack_api_name -m https://raw.github.com/digitalnz/supplejack_template/master/supplejack_api_template.rb
```
**Note:** Be sure to note down the user API key that is printed to the terminal during the install process.

This will create a new Rails app with the Supplejack API engine configured. After running the command you will be able access http://localhost:3000/records.json?api_key=_USER-API-KEY_ in your browser to view records from your API.  

### Installing the Supplejack Manager and Supplejack Worker

**Prerequisites** 

- The MongoDB database is running.  
- Supplejack API installed and running.  

##### This section is NOT complete and will be updated soon...

Clone the [Supplejack Manager](https://github.com/DigitalNZ/supplejack_manager) and the [Supplejack Worker](https://github.com/DigitalNZ/supplejack_worker) from Github. You require both applications as the Supplejack Manager creates jobs in the Supplejack Worker to process jobs in the background. 

In simple terms, the Supplejack Manger has two purposes. 
1. Create and manage [Parsers](supplejack/manager/parser-dsl-domain-specific-language.html).
2. Create and mange jobs which the Supplejack Worker will process.

In order for the Supplejack Manager to create the jobs it requires some information about the Supplejack Worker and vice versa. This information is stored in config/application.yml. We have provided an example for your reference in each application. You will also need to update the WORKER_API_URL and HARVESTER_IPS values in your API application.yml

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

Once all the applications have been configured you can then run all of them locally to test harvesting. We recommend running each application on a different port to avoid conflicts.

```bash
# From /path/to/api
bundle exec rails s 

# From /path/to/manager
bundle exec rails s -p 3001

# From /path/to/worker
bundle exec rails s -p 3002
```

The final step is to run sidekiq within the worker directory you can do this by running the following command:

```
bundle exec sidekiq
```

