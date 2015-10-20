---
layout: page
title: "Troubleshooting"
category: start
date: 2014-05-21 15:02:56
order: 9
---

## Remove stuck workers from Sidekiq

If [Sidekiq web interface](http://locahost:3002/sidekiq) shows workers which are processing failed jobs, you need to delete the jobs from `redis`.

```js
$ redis-cli
 > smembers workers
 > srem workers <each worker above>
 > smembers workers // should be empty
```

Refresh the web interface and the stuck workers should be gone.
