---
layout: page
title: "Usage Metrics"
category: metrics
date: 2015-08-13 17:16:19
---

Supplejack has an option to log metrics for record views. This can be [configured](/supplejack/start/supplejack-client.html) from the supplejack client. As of now only one field from the record schema can be logged for Usage Metrics.

A model called RequestLog tracks intermediate metrics data. There are three type of requests get, search and user set view. For each request of this type a new RequestLog model is created.

## RequestLog Model

Currently RequestLog has only two fields.

```ruby
  field :request_type,  type: String
  field :log_values,    type: Array
```

The RequestLog models that are created on each request are summed up to create the UsageMetrics. This is handled by a worker named UsageMetricsWorker. A resque pool and scheduler needs to be configured in the api app for this to function.

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

This table is populated when the worker runs and collates the existing RequestLog models.

```ruby
	field :record_field_value,  type: String
	field :searches,         		type: Integer
	field :gets,             	  type: Integer
	field :user_set_views,   	  type: Integer
	field :total,               type: Integer
```

## When overriding the controllers

If the standard Supplejack controllers are overridden, the following lines of code will need to be aded to Records and UserSet controllers. 

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

If the field thats configured in the supplejack configuration is wrong or if a record can't access the field mentioned, the api will log a warning in the API log

```ruby
  "[RequestLog][Warning] Field <field name> does not exist"
```
