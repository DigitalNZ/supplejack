---
layout: page
title: "Parser DSL (Domain Specific Language)"
category: manager
date: 2014-05-01 16:12:38
order: 2
---

## Records source
### base_url
The base_url method allows the operator to specify where to fetch the harvest resources from. It accepts a URL or a absolute path in disk. Additionally the operator can specify different urls/paths for each environment.

## proxy
The proxy method allows the operator to specify a proxy that the base_url needs to be requested through.

### Web resource
```ruby
base_url "http://gdata.youtube.com/feeds/api/videos"
```

### Path
```ruby
base_url "file:///data/sites/harvester/resources/nz_museum.xml"
```

### Different paths per environment
```ruby
base_url staging: "file:///data/sites/harvester/staging/nz_museum.xml"
base_url production: "file:///data/sites/harvester/production/nz_museum.xml"
```

### All the files in a directory
```ruby
Dir.glob('/export/home/harvest/nlnzcat/updates/*').sort.each do |filename|
  base_url "file://#{filename}"
end
```

## Authentication

### basic_auth
Allows to the operator to add HTTP Basic Authentication to all requests executed by the parser applied to it.

The first string is the username and the second is the password.


```ruby
basic_auth "username", "password"
```

This will basically append "username:password" to the URL's of the requests executed by this parser. So a request to: "http://gdata.youtube.com/feeds/api/videos" will be converted to "http://username:password@gdata.youtube.com/feeds/api/videos"

### http_headers
Allows the operator to add http headers to the request, so that requests can be made to protected endpoints.

It can be used like

```ruby
http_headers({'x-api-key': 'api-key', 'Authorization': 'Token token="token"'})
```

## Pagination
### paginate

Allows the operator to paginate through a API and specify the name of the parameters used by the particular API as well as the values for those parameters.

#### Numbered pagination

```ruby
base_url "http://gdata.youtube.com/feeds/api/videos"
paginate page_parameter: "start-index", type: "item", per_page_parameter: "max-results", per_page: 50, page: 1, total_selector: "//openSearch:totalResults"
```

#### Tokenised pagination

Tokenised pagination is enabled for XML, OAI, and JSON parser scripts.

```ruby
base_url "http://gdata.youtube.com/feeds/api/videos"
  paginate type: 'token', next_page_token_location: "$.nextPageToken", per_page: 1, per_page_parameter: "maxResults", page_parameter: 'pageToken'
```

The above example will execute the following requests:

```
http://gdata.youtube.com/feeds/api/videos?start-index=1&max-results=50
http://gdata.youtube.com/feeds/api/videos?start-index=51&max-results=50
http://gdata.youtube.com/feeds/api/videos?start-index=101&max-results=50
etc...
```

The total_selector option is to extract the total amount of records so that the paginator knows when to stop.  For tokenised pagination, this is an optional parameter.  Without it, the harvest will stop when the next_page_token is not present.

The type option refers to the weather the pagination is implemented by specifiying the starting index like in the above case where you need to specify "item" or the case where you specify the actual page value, for this case use the value "page".  Use `type: 'tokenised'` for tokenised pagination.

For apis that require an initial parameter for the first tokenised paginated request, but not for successive requests, you can use the `initial_parameter: 'abc'` method.

#### Scroll Harvest

You can harvest from an Elastic Search scroll harvest endpoint by providing `paginate type: "scroll"` in your parser. The parser is expecting the first request to be a POST request so make sure that your base_url is given accordingly. The harvest will automatically page to subsequent pages via the header location that is returned from each page. You do not have to do anything extra. All provided http_headers will come through as expected.

Here is an example:

```
base_url "https://data.tepapa.govt.nz/collection/search/_scroll/?q=&size=50"
http_headers({'x-api-key': '<YOUR_API_KEY>'})
paginate type: "scroll"
```

## Reject records
### reject_if

The operator can use the reject_if directive to reject records which match the criteria specified.

```ruby
reject_if do
  get(:title).find_with(/Weekly Review/).present?
end
```

## Delete records
### delete_if

The operator can use the delete_if directive to mark records as deleted in the api which match the criteria specified.

```ruby
delete_if do
  get(:title).find_with(/Weekly Review/).present?`
end
```

When the block of code returns a true value the record will be marked as deleted in the API. In the particular case above all records with a title that matches "Weekly Review" will be deleted.

## Throttling
### throttle

To throttle the requests to a specific host add a throttle directive to the parser configuration.

```ruby
class TestParser < HarvesterCore::Xml::Base
  throttle :host => "gdata.youtube.com", :delay => 10
end
```

The effect of the throttle directive above will be that requests to the gdata.youtube.com host will be at least 10 seconds apart from each other.

Fractions of a second are also allowed for finer grained control.

## Request timeout
### request_timeout

To change the length of time that the harvester will wait before timing out a request you can do the following:

```ruby
class TestParser < HarvesterCore::Xml::Base
  request_timeout 60000
end
```

_Note:_ The time is in milliseconds, so the above example will set a request timeout of 60 seconds

## Harvesting non-primary sources
### priority

By defining a priority, the harvest operator indicates that this parser will not overwrite the primary source, but will create an additional source on the record with a matching internal identifier. The priority is a positive or negative integer, a value of 0 will disable this feature (overwrite the primary source).

```
priority 5
```

Unintuitively, sources with more negative priorities are used first, and more positive priorities are used last. This could also be expressed as higher priorities are lower priority.


## Concepts
### Matching

To specify the type of concept matching required, set the @match_concepts@ directive as below.

```
match_concepts :create_or_update
```

It can be set to:

* `:create` which doesn't perform any matching, always creating new concepts
* `:match` which will only match (and update the sameAs field on the matching concept with data from this harvest)
* `:create_or_update` which will match if it exists, otherwise create a new concept.

## XML Specific
### Record Selector

When the source of the records is in one big XML file it is necessary to split the document into XML fragments that represent each record.

```ruby
class TestParser < HarvesterCore::Xml::Base
  record_selector "//item"
end
```

The "//item" string represents the xpath that will split the XML file.

### Sitemap Entry Selector

In cases where the base_url points to a sitemap, the operator can specify the xpath that matches each URL in the sitemap.

```ruby
class TestParser < HarvesterCore::Xml::Base
  sitemap_entry_selector "//loc"
end
```

The most common use case for this is sitemaps and the typical node where sitemaps store the URL is a node.

### Record Format

When harvesting from a sitemap the most common case is that the resources specified in the sitemap are HTML web pages, but there are cases when they are XML.

For these cases it is necessary to specify what is the format of the web resources.

```ruby
class NzOnScreen < HarvesterCore::Xml::Base
  sitemap_entry_selector "//loc"
  record_format :xml`
end
```

### Multiple Records from multiple Sitemap entries

Sometimes a sitemap_entry_selector will specify a location that specifies multiple records, in this case you will need to use all 3 of the above attributes in conjunction with each other, for example:

```ruby
class NzOnScreen < HarvesterCore::Xml::Base
  sitemap_entry_selector "//loc"
  record_selector "//feed//entry"
  record_format :xml`
end
```

In this instance the sitemap entries are nodes. At each location specified by a sitemap entry there are multiple record entries which can be selected by specifying the record_selector. In this instance the expected format of the records entries is XML.

### Namespaces

_See XML Namespaces_ --  link to namepaces page soon

The harvest operator can define namespaces at the class level. This allows the operator to then specify which namespace it wants to use for specific xpath queries.

```ruby
class NzOnScreen < HarvesterCore::Xml::Base
  namespaces dc: "http://purl.org/dc/elements/1.1/"
  attribute :category, xpath: "//dc:identifier"
end
```

### Node

Sometimes the data will contain groups of tags which relate to each other. For example a number of authors, each with a first name and last name. In this case the harvest operator can select the group node and then access the children in a block.

```ruby
attribute :contributor do
  contributor = get(:contributor)

  node("//person").each do |node|
    contributor += compose(node.xpath("first-name").text, " ", node.xpath("last-name").text)
  end

  contributor
end
```

If you are wanting to use predefined namespaces inside of a node block you will need to add self._namespaces to all your xpath method calls. This is because within the node block the node is just a nokogiri element.

```ruby
node("./metadata/m:record/m:datafield[@tag='260']").each do |node|
  text << node.xpath("./m:subfield", self._namespaces).map(&:text).join("; ")
end
```

## OAI Specific
(this line seems wrong - the default OAI implementation should not specify a "set" at all)

By default, the OAI implementation uses &metadataPrefix=oai_dc with no set value, but these can be configured on a per-parser basis, as below:

### MetadataPrefix

```ruby
base_url "http://emu.tepapa.govt.nz/oai/oai.php"
metadata_prefix 'oai_dc_mm'
```

### Set

```ruby
base_url "http://emu.tepapa.govt.nz/oai/oai.php"
set 'CEISMIC'
```

## JSON Specific
### Record Selector

When there are many records in one JSON file, it is necessary to select the array containing records.

```ruby
class TestParser < HarvesterCore::Json::Base
  record_selector "$.items"
end
```

This is a JsonPath expression that selects the array. If the root of the file is an array, you would use "$"

### Paths

Attributes are selected within each record using JsonPath

```ruby
attribute :title, path: "$.title"
attribute :author, path: "$.author.name"
```

See http://goessner.net/articles/JsonPath/ for more details on JSON path.

Note: JSON key names containing special characters like : and similar should be surrounded in 'single quotes', even if they are the old thing in the path. For example:

```ruby
attribute :title, path: "'dc:title'"
# or
attribute :title, path: "$.'dc:title'"
```

## Preprocess source data before running the parser script
This optional block allows manipulation of the response data from your harvest source, before it is handed on to the rest of the parser as per normal. It could be used for any type of pre-processing data clean up requirements but was initially designed to rationalise verbose feeds that mentioned items multiple times, keeping only the latest mention to be harvested.
JSON example

```ruby
pre_process_block do |rest_client_response|
  # Convert RestClient::Response to Hash
  hash = JSON.parse(rest_client_response.body)

  # Sort and uniq the data will result in only the latest of each item
  hash = hash.sort do |item_a, item_b|
    # 'updated_at' specifies the date to sort on
    Date.parse(item_b['updated_at']) <=> Date.parse(item_a['updated_at'])
  end
    .uniq { |item| item['audio_id'] } # 'audio_id' specifies the unique item ID to rationalise with

  # Convert back to JSON
  json = hash.to_json

  # Return a new RestClient::Response with the new mutated JSON
  RestClient::Response.create(json, rest_client_response.net_http_res, rest_client_response.request)
end
```

XML Base example
```ruby
pre_process_block do |rest_client_response|
  # Convert RestClient::Response to Nokogiri Document
  doc = Nokogiri::XML(rest_client_response.body) { |config|    config.options = Nokogiri::XML::ParseOptions::NOBLANKS }

  # Select node that contains all items
  items_node  = doc.at_xpath('//dnz-export')

  # Sorting by the "date" field
  sorted = items_node.children.sort_by do |item|
    item.children.find { |child| child.name == 'date' }.text
  end.reverse!

  # uniq will keep only the latest mention of each item based on the unique ID of that item (specified in "key")
  uniq = sorted.uniq do |item|
    item.children.find { |child| child.name == 'key' } .text
  end

  # Replace all children with new values
  items_node.children.remove
  uniq.each{ |n| items_node << n }

  # Return a new rest response
  RestClient::Response.create(doc.to_xml, rest_client_response.net_http_res, rest_client_response.request)
end
```
