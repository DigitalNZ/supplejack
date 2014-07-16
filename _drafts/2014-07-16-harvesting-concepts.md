---
layout: page
title: "Harvesting concepts"
category: manager
date: 2014-05-01 16:01:30
order: 10
---

Harvesting concepts is similar to harvesting normal records. You need to have a working Supplejack API with concept schema. For more information on how to set up concept schema, please refer to <link>.

## Configuration

To start harvesting concepts, you need to enable concepts in the Manager. `config/application.yml`.

```yaml
development:
  WORKER_HOST: http://localhost:3002
  WORKER_API_KEY: 5dFn4V9r63BCNpK6TsaU
  API_HOST: http://localhost:3000
  API_MONGOID_HOSTS: localhost:27017
  PARSER_TYPE_ENABLED: true # Set this to true
```

Restart the Manager.

Creating new parser will give you another option to select a data type.

![Parser Data Type](images/supplejack-manager-concept-datatype.png)





