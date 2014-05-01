---
layout: page
title: "Dependancies"
category: doc
date: 2014-05-01 15:02:05
order: 1
---

Supplejack requires the following to run the full stack. Please review this page carefully as some of these dependancies are version specific and failure to use the correct versions will cause problems.

### Ruby

**Version: 1.9.3**

**Description**: Ruby is the language we use for all of the supplejack components. This is because we use the ruby on rails framework.

**Installation instructions:** We reccomend using RBENV to manage your ruby versions here is some information about how to install ruby with rbenv: https://github.com/sstephenson/rbenv.

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

**Installation instructions:** Most of the installation of SOLR is already done for you when you bundle the Supplejack API application. The sunspot-solr gem comes with an older version of SOLR so it's just a matter of upgrading the version of SOLR that sunspot-solr is using.  
**Note:** You will have to upgrade SOLR after you have completed the installed process. You can find information on [how to do that here](https://github.com/sunspot/sunspot/wiki/Upgrading-sunspot_solr-Solr-Instance).
