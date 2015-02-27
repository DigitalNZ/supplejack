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

**Description**: The Rails web framework and all Supplejack components are written in Ruby.

**Installation instructions OSX/Linux/Unix:**

We strongly recommend using [rbenv](https://github.com/sstephenson/rbenv) to manage your ruby versions. OSX and *nix users can install rbenv from Github checkout: https://github.com/sstephenson/rbenv#installation

**Alternative installation for OSX using _Homebrew_:**

OSX users should consider an alternative mode of installation using the [Homebrew](http://brew.sh/) package manager.

```
$ brew update
$ brew install rbenv ruby-build
$ echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
```

Installing rbenv with Homebrew notes: https://github.com/sstephenson/rbenv#homebrew-on-mac-os-x

**Installation instructions Windows:**

You can use RubyInstaller to set up a full Ruby development environment on Windows.

For more information on how to install ruby, you can refer to official ruby guide https://www.ruby-lang.org/en/installation/#rubyinstaller

### Rails

**Version: 4.1.7**

**Description**: Rails is a full-stack open source web application framework.

**Installation instructions:** http://rubyonrails.org/download/

If you are using [rbenv](https://github.com/sstephenson/rbenv), simply enter the following command:

`gem install rails -v '4.1.7'`

### Mongo

**Version: 2.2 or greater**

**Description**: Supplejack uses mongo as a data store for all of it's components. You can find out more about mongo on their website or on the wikipedia page.

**Installation Instructions:** http://docs.mongodb.org/manual/installation/.

**Alternative installation for OSX using _Homebrew_:**
```
$ brew update
$ brew install mongodb
```

### Redis

**Version: 2.4 or greater**

**Description:** Supplejack uses redis for sidekiq and for resque as they both require redis as a concurrency safe data store. You can find out more about redis on their website or on the wikipedia page.

**Installation instructions**: http://redis.io/download

**Alternative installation for OSX using _Homebrew_:**
If you are using Mac OSX we would recommend using brew for installing redis with the following command:

```
$ brew update
$ brew install redis
```

### Java

**Version: 6 or greater**

**Description:** Java is a general purpose programming language. The Solr search platform is written in Java.

**Installation instructions:** https://www.java.com/en/download/help/download_options.xml

**Installation instructions for Mac:** http://support.apple.com/kb/DL1572

### Solr

**Version: 4.1**

**Description:** SOLR is the full-text search platform that underpins the Supplejack Records API.

**Installation instructions:** 

A development version of Solr is installed  for you when running `bundle install` as it is included in the [sunspot_solr gem](https://github.com/outoftime/sunspot/tree/master/sunspot_solr). For production installation see **Production Install** 
