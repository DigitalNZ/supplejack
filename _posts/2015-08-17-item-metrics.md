---
layout: page
title: "Item Metrics"
category: metrics
date: 2015-08-17 11:52:56
---
Supplejack can be configured to generate metrics about the number of records in the document store. It will only work out-of-the-box if you use the generic schema provided. The generic schema will provide reporting broken down by display collection (which is then further broken down by category and copyright). The worker that generates these metrics can be run in two modes, full and incremental.

### Different modes

If the worker is run in full mode then it will do a full scan of all the records in the database which can be quite lengthy (around an hour with 10 million records) but it is only required for the initial run, after that incremental runs will be automatically performed. An incremental run only checks the records that were created on a single day, instead of all the records in the database, reusing the previous days item metrics counts. Incremental runs are signifigantly faster, with a runtime measured in seconds instead of hours.

### Modifying the tracked metrics

The field that records get grouped on (primary\_key) and the fields that get counted (secondary\_keys, these must be multivalue fields, eg an array of values) can be configured in `app/workers/supplejack_api/daily_item_metrics_worker.rb`

Changing the secondary keys will require modifying the DisplayCollectionMetric model (`app/models/supplejack_api/display_collection_metric.rb`), it must have a field in the form of `"#{secondary_key_name}_counts"` for each of the secondary\_keys

The metrics are queried using the MongoDB map reduce framework, depending on how different the fields are from the originally tracked ones some understanding of the map reduce framework is required.  

If your schema does not contain the display\_collection, category and copyright fields (or if category and copyright have been changed from multi value to single value fields) then the map reduce query will require updating.