---
layout: page
title: "Link Checker"
category: manager
date: 2014-05-21 11:53:13
order: 9
---

Overview
--------

The link checker is a component of the the DNZ harvesting stack. It's
primary purpose is to ensure that records's `landing_url` are still
available, thus ensuring that the overall quality of records is high.

In regard to the link checker, whenever `collection` is used, it refers
to the `primary_collection`.

Components
----------

### Source Checking (On Demand)

When a `source_url` (such as
[http://api.digitalnz.org/records/23141109/source](http://api.digitalnz.org/records/23141109/source))
is accessed, the API submits the link to the link checker.

This gets processed according to the [Link Checking Report
page](http://localhost:3001/staging/collection_statistics) for that
collection. See below.

Once the rule has been run against the record, it's state (`active` or
`supressed`) will be updated if necessary.

### Collection Checking

Every 2 hours, the 2 oldest and 2 newest records, by syndication date,
from each collection are checked. If all records fail their collection
rules (below), the collection is marked as `suppressed`. After 3 further
attempts (so 4 in total) with exponential back-off, the collection is
marked as `deleted`.

### Manually Suppress Collection

If a harvest operator knows that a specific collection is going to be
down or if they need to remove the collection's items from the API they
can do so via the [Suppress
Collections](http://harvester.uat.digitalnz.org/suppress_collections)
interface of the harvester. An operator can see which collections are
suppressed, and add or remove collections.

*Note: Marking a collection as suppressed doesn't delete the collections
items (and are thus still available if you their id), they are just
added to the [API Suppressed Sources](http://localhost:3000/sources.json&source[status]=suppressed)
and don't show up in search results.*

### Collection Rules

The Collection Rule defines what it means for an item to inaccessible.
The Collection Rule defines:

* Collection title: the `primary_collection` for which this rule
applies to
* Xpath: If this expression evaluates as true or returns elements
against the returned document, the record is deemed to be inactive. Examples:
    * `//p[@class="error"]`
    * `//a[@href="#intro"]/h1/span`
    * `//h1/span[text()[contains(.,"boost")]]`
* Status codes: If any of these comma-separated, regular expression
status codes match the HTTP status code returned, the record is deemed
to be inactive. 404 is assumed to be an invalid code, so doesn't need to
be added explicitly. Examples:
    * `301,302` 
    * `301,4..,50.`
* Throttle: The delay (in seconds) between requests being sent to the
content partner. Analogous with `throttle` in the [parser
DSL](http://digitalnz.github.io/supplejack/manager/parser-dsl-domain-specific-language.html)

* Collection link checking active?: This disables link checking
activities for that collection - meaning that 404 errors will keep the
record active.

### Network Health Awareness

The link checker is aware of it's internet connectivity. It attempts to
get the `www.google.com` homepage every 5 minutes, and if it isn't
successful a global `active` flag is un-set. This means that if there is
an issue with internet connectivity, DNS or other network issues the
link checked doesn't bring down all collections in one fell swoop.

### Logging

Statistics on the number of links activated, suppressed and deleted on a
given day are shown on the Manager [Link Checking Report
page](http://localhost:3001/staging/collection_statistics).
This is also emailed to Harvest Operators

Nitty Gritty Technical Details
------------------------------

### Relationship between the Manager, the API and the Worker

The Manager houses all the setup of the collection checking and the
rules. The Manager provides an interface to the [API Suppressed Sources](http://localhost:3000/sources.json&source[status]=suppressed)
to manually suppress and active whole collections. The work to actually
perform the checking of the `landing_url`s and to check the health of the
network is all done in the Worker, running as Sidekiq jobs.
