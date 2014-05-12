---
layout: page
title: "Supplejack Worker"
category: start
date: 2014-05-01 15:01:48
order: 5
---

The harvester worker is a rails application that uses [Sidekiq](http://sidekiq.org/) to run all of the various jobs that occur in the harvesting and link checking process.

### Harvesting jobs
These use the harvester core gem to interpret a given parser which it uses to generate records and posts them to the Supplejack API (this is a simplified overview of the process).

### Link checking jobs
There are two kinds of link checking. Collection checking checks a few records from a collection and suppresses collections which are unavailable. Record checking checks individual records which have been visited recently (in the API) and suppresses records which are unavailable.

### Installation
1. Clone the worker from GitHub.
`git clone https://github.com/DigitalNZ/supplejack_worker.git`

2. Modify `config/application.yml` file.
* `API_HOST` - Host URL of your API 
 `e.g. http://localhost:4000`
* `MANAGER_HOST` - Host URL of the manager 
 `e.g. http://localhost:4001`
* `MANAGER_API_KEY` - An authentication key in order to authorize incoming requests from Worker.
* `LINK_CHECKING_ENABLED` - Set to true if you want link checking enabled
* `LINKCHECKER_RECIPIENTS` - Emails of recipients who will receive link check notifications
* `API_MONGOID_HOSTS` - Host URL of your MongoDB. Defaults to `localhost:27017`

### Generate Worker User keys

1. From the Worker's project root, Run `rails c`.
2. Create your first user 
  `User.create!`.
3. Copy the generated `authentication_token`.
4. Paste it inside your Manager's `application.yml` as `WORKER_API_KEY`.
