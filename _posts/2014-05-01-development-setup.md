---
layout: page
title: "Development Setup via Script"
category: start_script
date: 2014-05-01 15:02:23
order: 2
---

Once you have installed all the [dependencies](/supplejack/start/dependencies.html) you are now ready to install the rest of the stack. As all the components require configuration to interact we strongly recommend that you use the [installation template](https://github.com/DigitalNZ/supplejack_installation) which is a [Rails Application Template](http://guides.rubyonrails.org/rails_application_templates.html) which is provided for Supplejack.

If you are a Mac user using [Homebrew](http://brew.sh/) we strongly recommend you cleanly run brew doctor before installing.

```
$ brew update
$ brew doctor
```

Resolving issues that `brew doctor` picks up will alleviate many problems you might encounter installing Supplejack.

Please also note there is a known installation issue involving the _libv8_ gem, which can affect Yosemite users. We are working to resolve this.

## Start MongoDB
`mongod` is the primary daemon process for the MongoDB system. It handles data requests, manages data access, and performs background management operations. You need to run mongod in order to install Supplejack and whenever Supplejack is in use.

Assuming your installation went smoothly and your paths are set up correctly you should be able to run mongod from the command line.

```
$ mongod
```

If mongod doesn't start, review the [MongoDB installation documention](http://docs.mongodb.org/manual/tutorial/) for your system.

## Install the Supplejack Stack

The Supplejack Stack refers to the Supplejack API, Supplejack Manager and the Supplejack Worker applications.

The  [installation template](https://github.com/DigitalNZ/supplejack_installation) creates a new Rails application using the Supplejack API and then installs and configures the Supplejack Manager and Supplejack Worker applications.

To install the full stack run the following command:

```bash
# You should replace 'mysupplejack_api_name' with the name of your app.

$ rails _5.1.4_ new mysupplejack_api_name --skip-bundle -m https://raw.github.com/digitalnz/supplejack_installation/master/supplejack_api_template.rb
```
**Note:** Be sure to record the user API key that is printed to the terminal during the install process.

**Note:** You may also want to update the REQUEST_LIMIT_MAILER setting in `mysupplejack_api_name/config/application.yml` to an email address that you manage to receive admin/service alerts.)

This operation will create three directories and many files:
```
/mysupplejack_api_name
/supplejack_manager
/supplejack_worker
```

If you ever want to remove this supplejack project, simply delete these three directories.

```
$ cd mysupplejack_api_name
$ bundle install
```

## Configure Sidekiq
Supplejack uses Sidekiq to manage indexing jobs. Make sure you have installed Redis as Sidekiq depends on it.

To start Sidekiq, run the following command

```
$ cd mysupplejack_api_name
$ bundle exec sidekiq
```
These commands will start Sidekiq and have it start listening for jobs to process from Redis

## Configure Solr

Supplejack uses the [sunspot](https://github.com/sunspot/sunspot) gem to interact with Solr. There is currently an issue with Sunspot and Solr 4. For development instances, [please follow these configuration instructions](https://github.com/sunspot/sunspot/wiki/Upgrading-sunspot_solr-Solr-Instance). When asked which version of Solr to download, select [solr-4.1.0.tgz or solr-4.1.0.zip ](http://archive.apache.org/dist/lucene/solr/4.1.0/).

Confirm that you have configured Solr by starting an instance and visiting the admin dashboard at either: http://localhost:8982/solr or http://localhost:8983/solr.

Whenever you want to start Solr execute the following command from `/mysupplejack_api_name`.

```
$ bundle exec rake sunspot:solr:start
```

## Start Supplejack

Finally start a rails server from within `/mysupplejack_api_name`.

```
$ rails server
```


Once complete you will be able to access your API (at http://localhost:3000/records.json) and harvest new data from external sources.
