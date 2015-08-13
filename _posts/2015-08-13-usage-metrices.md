---
layout: page
title: "Usage Metrics"
category: metrices
date: 2015-08-13 17:16:19
---

Supplejack has an option to log the usage of data. This can be [configured](/supplejack/start/supplejack-client.html) from the supplejack client. As of now only one field from the record schema can be logged to create an Usage Matrics.

A module named RequestLog logs the usage of data. There are three type of requests namely get, search and user set view. This module is called into action on every search and view of records.

## RequestLog Model

Currently request log only has two fields.

```ruby
  field :request_type,  type: String
  field :log_values,    type: Array
```

The data logged on request log table are to summed up to create the UsageMetrics. This is handled by a worked module named UsageMetricsWorker. A resque pool and scheduler needs to be configured at the api app for this.

## Resque scheduler config

A time frame suitable can be selected.

```yaml
# Example Supplejack Worker resque_schedule.yml file

usage_metrics_scheduler:
  every: 60m
  class: 'SupplejackApi::UsageMetricsWorker'
  queue: usage_metrics
  description: "This job will create usage metrics entried and delete data from request log"
```

## UsageMetrics Model

This table is populated when the worker runs.

```ruby
	field :record_field_value,  type: String
	field :searches,         		type: Integer
	field :gets,             	  type: Integer
	field :user_set_views,   	  type: Integer
	field :total,               type: Integer
```

## When overriding the controllers

If the standard Supplejack controllers are to be overridden, developer will have to add few lines of code to records controller and user set controller. 

```ruby
# index method in RecordsController
	@search = SupplejackApi::RecordSearch.new(params)
	SupplejackApi::RequestLog.create_search(@search, params[:request_logger_field]) if params[:request_logger]

# show method in RecordsController
	@record = SupplejackApi::Record.custom_find(params[:id], current_user, params[:search])
  SupplejackApi::RequestLog.create_find(@record, params[:request_logger_field]) if params[:request_logger]

# show method in UserSetsController
  @user_set = UserSet.custom_find(params[:id])
  SupplejackApi::RequestLog.create_user_set(@user_set, params[:request_logger_field]) if params[:request_logger]
```

## Errors

If the field thats configured on the supplejack configuration is wrong or if a record cant access the field mentioned, the api will throw a warning on the API log

```ruby
  "[RequestLog][Warning] Field <field name> does not exist"
```