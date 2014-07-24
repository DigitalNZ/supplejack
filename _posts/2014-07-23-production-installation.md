---
layout: page
title: "Production Installation"
category: start
date: 2014-07-23 15:02:05
order: 3
---

Note: The documentation assumes the production has one server.

## Recomendations

1. Use Linux-based servers.
1. Use a configuration management tool, such as [Opscode Chef](http://www.getchef.com/chef/)
1. Use a process monitoring tool, such as Monit.

## 1. Install Ruby, Redis and MongoDB 

See [dependencies](/supplejack/start/dependencies.html) for details.

## 2. Install Supplejack Stack Applications

Install Supplejack Stack using [application template](http://digitalnz.github.io/supplejack/start/development-setup.html)

Note: the application template automatically starts development servers, which you don't want to run in production so shut them down.

1. Configure Supplejack Manager following this [guide](/supplejack/start/supplejack-manager.html).

+ **Configure Supplejack Worker**

Follow this [guide](/supplejack/start/supplejack-worker.html) to setup Worker.

+ **Configure API**

1. You need to set up schemas for Supplejack API. By default, a schema is created as part of the application template. See [Creating schemas](/supplejack/api/creating-schemas.html) To find out more about how to customize schemas, 

## 3. Install Java Development Kit(JDK)

Install Oracle JDK version `jdk-6u34` for Tomcat and Solr.

## 4. Install Solr and Tomcat
   
Solr needs to be installed inside a Servlet container for example Apache Tomcat. We use version `6.0.35.0`.

Insert [Solr config](https://github.com/DigitalNZ/supplejack_api/blob/master/spec/dummy/solr/collection1/conf/solrconfig.xml) to `SOLR_HOME/collection1/conf/`

## 5. Install and configure Apache with Passenger

1. Install Apache if not already installed.
1. Install Passenger module into Apache to run Rails applications.
1. Create proxy configurations for the API and Solr. 
1. Create `virtualhost` configurations for the manager and worker using Passenger.


## 6. Run Rack Web Server for the API

We suggest that you run 8 `thin` worker processes:

```
API_HOME/bin/thin start -d -u USER -g GROUP -p 8190 -s 8 -e production --stats='/t_stats'
```

## 7. Testing

1. Ensure Solr is running at http://HOST:8080
1. Ensure that the API is running at http://HOST/records/1.json?api_key=API_KEY
1. Ensure that Sidekiq is running at http://HOST/sidekiq
1. Ensure that the manager is running at http://harvester.HOST
1. Ensure that the worker is running at http://worker.HOST

