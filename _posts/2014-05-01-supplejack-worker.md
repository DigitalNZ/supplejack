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

### Preview Job
A preview job is initiated from the manager when a parser is previewed. The worker returns a snapshot of the how the records will look like when created.

### Link checking jobs
This job checks individual records which have been visited recently (in the API) and suppresses records which are unavailable.

### Source checking job
This job checks a few records from a collection and suppresses collections which are unavailable.

### Job priorities
There are 3 priority levels for Jobs in supplejack worker. Preview is the only `critical` priority job, which is the highest priority. Link Check job has `low` priority. All the other jobs are configured with `default` priority. 

### Supplejack API integration
Supplejack API harvester endpoints requires an API key with harvester privileges that is configured as enrironment variable. `HARVESTER_API_KEY`.

### Sidekiq
Jobs are processed by Sidekiq, which runs as a part of the worker. In order to start Sidekiq run `bundle exec sidekiq start`. You can then look at http://WORKER_HOST/sidekiq to view progress.  

If you are having issues with your Sidekiq worker jobs not running correctly you can add log output by following [these](https://github.com/javan/whenever/wiki/Output-redirection-aka-logging-your-cron-jobs) instructions. The most likely cause of issues is that the environment variables are not setup correctly for the cron script and the workers are unable to find the Ruby gems installed on the system

### Generate Worker User keys

From the Worker's project root, Create a user from the console:

```ruby
rails c
 > User.create!
=> #<User _id: 53714f99531163b56c000001, authentication_token: "RhymLHa9xRQGU8gyAYXP">
 > User.last.authentication_token
=> "RhymLHa9xRQGU8gyAYXP"
```

### Environment Configurations

```yaml
# Example Supplejack Worker application.yml file

development:
  API_HOST: "http://localhost:3000"
  HARVESTER_API_KEY: "#{api_key_with_harvester_privileges}"
  API_MONGOID_HOSTS: "localhost:27017"
  MANAGER_HOST: "http://localhost:3001"
  MANAGER_API_KEY: "#{manager_key}"
  HARVESTER_CACHING_ENABLED: true
  AIRBRAKE_API_KEY: "abc123"
  LINK_CHECKING_ENABLED: "true"
  LINKCHECKER_RECIPIENTS: "test@example.com"

production:
  API_HOST: "http://api.example.com"
  HARVESTER_API_KEY: "#{api_key_with_harvester_privileges}"
  API_MONGOID_HOSTS: "localhost:27017"
  MANAGER_HOST: "http://harvester.example.com"
  MANAGER_API_KEY: "MANAGER_API_KEY"
  HARVESTER_CACHING_ENABLED: true
  AIRBRAKE_API_KEY: "abc123"
  LINK_CHECKING_ENABLED: "true"
  LINKCHECKER_RECIPIENTS: "test@example.com"
```
