---
layout: page
title: "Troubleshooting"
category: start
date: 2014-05-21 15:02:56
order: 9
---

## Remove stuck workers from Sidekiq

If [Sidekiq web interface](http://localhost:3002/sidekiq) shows workers which are processing failed jobs, you need to delete the jobs from `redis`.

```js
$ redis-cli
 > smembers workers
 > srem workers <each worker above>
 > smembers workers // should be empty
```

Refresh the web interface and the stuck workers should be gone.

## Forbidden 403 errors on Manager when harvesting records or previewing

Check if your `harvester` user `authentication_token` in your Supplejack API
`SupplejackApi::User.where(role: 'harvester')`

Make sure the same `authentication_token` is set for your Manager and Worker Environment variables
`HARVESTER_API_KEY: "harvester access token"`
