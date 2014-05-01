---
layout: page
title: "Supplejack Worker"
category: doc
date: 2014-05-01 15:01:48
order: 5
---

The harvester worker is a rails application that uses [Sidekiq](http://sidekiq.org/) to run all of the various jobs that occur in the harvesting and link checking process.

### Harvesting jobs
These use the harvester core gem to interpret a given parser which it uses to generate records and posts them to the Supplejack API (this is a simplified overview of the process).

### Link checking jobs
There are two kinds of link checking. Collection checking checks a few records from a collection and suppresses collections which are unavailable. Record checking checks individual records which have been visited recently (in the API) and suppresses records which are unavailable.
