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

## Pagination
### paginate

Allows the operator to paginate through a API and specify the name of the parameters used by the particular API as well as the values for those parameters.  

```ruby
base_url "http://gdata.youtube.com/feeds/api/videos"
paginate page_parameter: "start-index", type: "item", per_page_parameter: "max-results", per_page: 50, page: 1, total_selector: "//openSearch:totalResults"
```

The above example will execute the following requests:  

```
http://gdata.youtube.com/feeds/api/videos?start-index=1&max-results=50
http://gdata.youtube.com/feeds/api/videos?start-index=51&max-results=50
http://gdata.youtube.com/feeds/api/videos?start-index=101&max-results=50  
etc...
```

The total_selector option is to extract the total amount of records so that the paginator knows when to stop.  

The type option refers to the weather the pagination is implemented by specifiying the starting index like in the above case where you need to specify "item" or the case where you specify the actual page value, for this case use the value "page".  

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
