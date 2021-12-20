---
layout: page
title: Authentication
category: api_usage
date: 2021-12-20 12:00:00
---

Each user has an API key. You need to provide this API key with the header `Authentication-Token`. Here's an example with curl:

```bash
curl -H 'Authentication-Token: <API_KEY>' https://api.example.com/
```

Depending on how your app is configured you'll need this API key or not. A user/api key is attached to a role. The role defines what read/write actions you can do on every resources.
