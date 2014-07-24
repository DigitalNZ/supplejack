---
layout: page
title: "Supplejack API"
category: start
date: 2014-05-01 15:00:53
order: 3
---

The Supplejack API is a [mountable engine](http://guides.rubyonrails.org/engines.html) that provides functionality to store, index and retrieve metadata via an API. It also includes an API dashboard for monitoring API key activity and setting query throttle rates.

This wiki provides a guide on how to create and configure your API as well as an introduction to the rest of the Supplejack project.

## Getting started

Before starting you should check that you have all the [dependencies](/supplejack/start/dependencies.html) installed.

Once that is complete we strongly recommend using the [Supplejack template](https://github.com/DigitalNZ/supplejack_installation) to create your app. This template will create a new app which includes the Supplejack API engine and then step through the configuration options. 

For details about how to install Supplejack Template, see [Install & Setup](/supplejack/start/install-setup.html)

Once the install is complete you should have a working API. The next step is to [configure your schema](/supplejack/api/creating-schemas.html) so that you can configure the fields that are stored/returned by your API.
