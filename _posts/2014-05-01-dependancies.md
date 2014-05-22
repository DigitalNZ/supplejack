---
layout: page
title: "Dependencies"
category: start
date: 2014-05-01 15:02:05
order: 1
---

Supplejack requires the following to run the full stack. Please review this page carefully as some of these dependencies are version specific and failure to use the correct versions will cause problems.

### Ruby

**Version: 1.9.3**

**Description**: Ruby is the language we use for all of the supplejack components. This is because we use the ruby on rails framework.

**Installation instructions:** We recommend using RBENV to manage your ruby versions here is some information about how to install ruby with rbenv: https://github.com/sstephenson/rbenv.

### Mongo

**Version: 2.2 or greater**

**Description**: Supplejack uses mongo as a data store for all of it's components. You can find out more about mongo on their website or on the wikipedia page.

**Installation Instructions:** http://docs.mongodb.org/manual/installation/.

### Redis

**Version: 2.4 or greater**

**Description:** Supplejack uses redis for sidekiq and for resque as they both require redis as a concurrency safe data store. You can find out more about redis on their website or on the wikipedia page.

**Installation instructions**: http://redis.io/download, if you are using Mac OSX we would recommend using brew for installing redis with the following command: `brew install redis`

### SOLR

**Version: 4.1**

**Description:** SOLR is used for full-text searching of records in supplejack.

**Installation instructions:** Solr is installed for you when running bundle install as it is included in the [sunspot_solr gem](https://github.com/outoftime/sunspot/tree/master/sunspot_solr).
