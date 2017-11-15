---
layout: page
title: "Dependencies"
category: start
date: 2014-05-01 15:02:05
order: 1
---

Supplejack requires the following to run the full stack. Please review this page carefully as some of these dependencies are version specific and failure to use the correct versions will cause problems.

**Note for OSX users**
We recommend OSX users use the [Homebrew](http://brew.sh/) package manager to manage the installation of Ruby,  MongoDB and Redis. If you're new to Homebrew, here is an [introduction](http://matthewcarriere.com/2013/08/05/how-to-install-and-use-homebrew/).

* We recommend performing `brew update` to update your Homebrew formula list. This does not update your installed packages. It simply updates the versions and availability of packages you could install. Although `brew update` is listed for each installation, you probably only need to run the command at the start of an installation session.
* Use `brew doctor` after system changes or if you need to troubleshoot your Homebrew installation.


### Ruby

**Version: 2.3.0**

**Description**: [Ruby](https://www.ruby-lang.org/) is a general purpose programming language. The Rails web framework and all Supplejack components are written in Ruby.

**Installation instructions OSX/Linux/Unix:**

We strongly recommend using [rbenv](https://github.com/sstephenson/rbenv) to manage your ruby versions. OSX and *nix users can install rbenv from Github checkout: https://github.com/sstephenson/rbenv#installation

Once rbenv is installed, you should [install Ruby 2.3.0](https://github.com/sstephenson/rbenv#choosing-the-ruby-version).

```
$ rbenv install 2.3.0
```

**Alternative _rbnenv_ installation for OSX using _Homebrew_:**

OSX users should consider an alternative mode of installation using the [Homebrew](http://brew.sh/) package manager. The rbenv github page [describes this process](https://github.com/sstephenson/rbenv#homebrew-on-mac-os-x), but the key commands are:

```
$ brew update
$ brew doctor
$ brew install rbenv ruby-build
$ echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
```

Once rbenv is installed, you should [install Ruby 2.3.0](https://github.com/sstephenson/rbenv#choosing-the-ruby-version).

```
$ rbenv install 2.3.0
```


**Installation instructions Windows:**

You can use RubyInstaller to set up a full Ruby development environment on Windows.

For more information on how to install ruby, you can refer to [official ruby guide](https://www.ruby-lang.org/en/installation/#rubyinstaller).


### Rails

**Version: 4.1.7**

**Description**: [Rails](http://rubyonrails.org/) is a full-stack open source web application framework.

**Installation instructions:** http://rubyonrails.org/download/

If you are using [rbenv](https://github.com/sstephenson/rbenv), simply enter the following command:

`gem install rails -v '4.1.7'`


### MongoDB

**Version: 2.2 or greater**

**Description**: Supplejack uses MongoDB as a data store. You can learn about MongoDB on its [website](http://www.mongodb.org/) or on the [Wikipedia](http://en.wikipedia.org/wiki/MongoDB) page.

**Installation instructions:** http://docs.mongodb.org/manual/installation/.

**Alternative installation for OSX using _Homebrew_:**

If you are using Mac OSX we recommend [installing MongoDB with Homebrew](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-os-x/).

```
$ brew update
$ brew doctor
$ brew install mongodb
```


### Redis

**Version: 2.4 or greater**

**Description:** Supplejack uses [Sidekiq](http://sidekiq.org/) for background processing and queue management. Sidekiq requires [Redis](http://redis.io/) as a concurrency safe data store.

**Installation instructions**: http://redis.io/download

**Alternative installation for OSX using _Homebrew_:**

If you are using Mac OSX we recommend using Homebrew to install Redis:

```
$ brew update
$ brew doctor
$ brew install redis
```


### Java

**Version: 6 or greater**

**Description:** [Java](http://en.wikipedia.org/wiki/Java_%28programming_language%29) is a general purpose programming language. The Solr search platform is written in Java.

**Installation instructions:** https://www.java.com/en/download/help/download_options.xml

**Installation instructions for OSX:** http://support.apple.com/kb/DL1572


### Apache Solr

**Version: 4.1**

**Description:** [Solr](http://lucene.apache.org/solr/) is the full-text search platform that underpins the Supplejack Records API.

**Installation instructions:**

A development version of Solr is automatically installed during [Supplejack installation](http://digitalnz.github.io/supplejack/start/development-setup.html).

For production installation see [Production Install](http://digitalnz.github.io/supplejack/start/production-install.html).
