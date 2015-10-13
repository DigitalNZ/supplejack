---
layout: page
title: "Item Metrics"
category: metrics
date: 2015-08-17 11:52:56
---
Supplejack can be configured to generate metrics about the number of records in the document store. It will only work out-of-the-box if you use the generic schema provided. The generic schema will provide reporting broken down by display collection (which is then further broken down by category and copyright).

The metrics are generated using the RecordSearch class to query the Solr index

### Modifying the tracked metrics

The field that records get grouped on (primary\_key) and the fields that get counted (secondary\_keys, these must be multivalue fields, eg an array of values) can be configured in `app/workers/supplejack_api/daily_item_metrics_worker.rb`

Changing the secondary keys will require modifying the FacetedMetrics model (`app/models/supplejack_api/faceted_metrics.rb`), it must have a field in the form of `"#{secondary_key_name}_counts"` for each of the secondary\_keys

If your schema does not contain the display\_collection, category and copyright fields (or if category and copyright have been changed from multi value to single value fields) then the map reduce query will require updating.
