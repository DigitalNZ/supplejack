---
layout: page
title: "Schema DSL (Domain Specific Language)"
category: api
date: 2014-05-02 16:28:49
order: 2
---

## Fields

Fields are defined using the following syntax:

```ruby
type name options do
  block
end
```

where:

* `type` is the type of the field. Must be one of [`string`, `integer`, `datetime`, `boolean`].
* `name` can be any valid ruby identifier (i.e. must not start with a number or a [Ruby reserved word](http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Lexicology#Reserved_Words)).
* `options` (all optional):
    * `search_boost` is an integer passed to Sunspot/Solr to increase the search relevance by the given factor. Default: `1`.
    * `search_as` determines whether the field can be searched a filter, fulltext, more like a record(mlt) or a combination of the 3. Valid values are `[:filter]`, `[:fulltext]`, `[:mlt]` or `[:filter, :fulltext, :mlt]`. Default: `[]`. Detailed explanation of these configurations are below.
    * `store` is a boolean value which determines whether the field is stored in the Mongo database or not. Default: `true`.
    * `multi_value` is a boolean value which determines whether the value is stored as an array or single value. Default: `false`.
    * `solr_name` is a string which is the name of the field in Solr. Default: field's `name`.
* `block`
    * `search_value` is a Ruby `Proc` which produces the value which should be indexed by Solr. The block is executed when the field is indexed. Must be the same type as the field's `type`. Default: `nil`.

```ruby
# Examples

string  :name,     search_as: [:filter, :fulltext]

string  :address,  search_as: [:fulltext]

string  :email,    search_as: [:fulltext], multi_value: true

# Create a full text field that is stored in SOLR but not in Mongo
string  :fulltext  do
  search_as: [:fulltext]
  store: false
  search_value do
    text = []
    [ :name, :address, :email
    ].each do |f|
      value = record.public_send(f)
      text << value if value.present?
    end
    text.flatten.join(" ")
  end
  solr_name :text
end 
```

### Search as configurations

#### fulltext
The fulltext option indexes the field so it can be searched using the text parameter. For example, using the fulltext option on a title and description field would allow records to be found by any words appearing in these two fields.

#### filter
The filter option indexes fields so that queries can be filtered on a specific value of that field.
Example query for `category` = `books`

#### mlt
This enables the "more like this" feature of Solr. when `[:mlt]` is configured with a field, Solr indexes the "relatedness of records" based on the fields with this option enabled.
Query the api to return similar records using `/records/$record_id/more_like_this`.

## Groups
Groups allow you to collect several fields together and reference them using a single value. 

Groups are a set of fields which are returned from the API. There _must_ be a single default group which is returned when no explicit group is requested.

The groups are exposed via the `fields` URL parameter, such as http://api.example.com/records/123?api_key=abc&fields=verbose

A group consists of:

* `name` is a ruby identifier, must be unique. _Required_    
* `default` is a boolean determining that this is the default group for a query.  
At least one of the following must exist:
* `fields` is an array of symbols defining the fields in that group. The fields must be defined (see above).
* `includes` is an array of symbols signifing that all fields from the given group(s) are included in this group.

```ruby
group :default, default: true do
  fields [
    :name,
    :address
  ]
end

group :all_fields do
  includes [:default]   # NOTE: Groups can include other groups
  fields [
    :age,
    :gender,
    :email,
    :phone
  ]
end
```

```javascript
// Sample requests with different groups
// api.supplejack.com/records/123.json?api_key=abc123
{
  "record": {
     name: "Joe Smith",
     address: "123 Smith St, Wellington, New Zealand"
  }
}

// api.supplejack.com/records/123.json?api_key=abc123&fields=all_fields
{
  "record": {
     name: "Joe Smith",
     address: "123 Smith St, Wellington, New Zealand",
     age: 32
     gender: "male"
     email: "joe@test.com"
     phone: "07 123 5457"
  }
}
```

## Roles

Roles are assigned to users, to attach field and record restrictions easily. There _must_ be a single default role which is used when a new user is created.

A role has field and restrictions. A field restriction means that the field value is hidden from the user, whereas a record restriction means that the whole record will be hidden from the user. This is useful if you have internal data (like URLs etc) which can't be given to external users.

The groups consists of:

* `name` is a ruby identifier, must be unique. _Required_
* `default` is a boolean determining that this is the default role for a new user.  
Both are optional:
* `field_restrictions` is a Hash where the key is the `field` to match, and the value is another Hash, the key of which is value of the `field` to match, and the value is the `field` is restricted. The value of `field` to match can either be a single `string` or a `RegEx`.
   * The example above restricts the `attachments` field when the `collection` is `NZ On Screen` and `large_thumbail_url` field when it includes `secret.com`.
* `record_restrictions` is a Hash. If the hash key is equal to the hash value for the record, then the record is restricted.
  * The example above restricts records where `access_rights` is `false` if the request's API key has a `developer` role.

## Mongo Indexing

By default Supplejack does not index fragments. You should set this up yourself.

All the fields that you define in the schema will be part of the Fragment. You shoul index those fields using the `mongo_index` DSL.

```ruby
mongo_index :status, fields: [{status: 1}]
```

See the main _Example Concept Schema_ example in [Creating Schemas](/supplejack/api/creating-schemas.html) for a demonstration of setting up Mongo Indexing.

## Record Fields

You can specify the fields that you want to be part of the Record model instead of the Fragment by using `model_field` DSL.

```ruby
model_field :landing_url, field_options: { type: String }, index_fields: { landing_url: 1 }, index_options: { background: true }, validation: { url: true }
```
