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
    * `search_as` determines whether the field is can be searched a filter, fulltext, or both. Valid values are `[:filter]`, `[:fulltext]` or `[:filter, :fulltext]`. Default: `[]`.
    * `store` is a boolean value which determines whether the field is stored in the Mongo database or not. Default: `true`.
    * `multi_value` is a boolean value which determines whether the value is stored as an array or single value. Default: `false`.
    * `solr_name` is a string which is the name of the field in Solr. Default: field's `name`.
    * `namespace` the namespace of this field.
    * `namespace_field` the field name from the namespace. Use if your field name is not the same as the defined in the namespace
* `block`
    * `search_value` is a Ruby `Proc` which produces the value which should be indexed by Solr. The block is executed when the field is indexed. Must be the same type as the field's `type`. Default: `nil`.

```ruby
# Examples

string  :name,     search_as: [:filter, :fulltext], namespace: :dc, namespace_field: :creator

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

## Namespaces

Define namespaces that relate to your fields. Each namespace has a url which refers to the documentation for that namespace.
Namespaces are used by the Serializer to build the @context block in the json-ld output.

```ruby
namespace :edm,    url: 'http://www.europeana.eu/schemas/edm/'
namespace :rdaGr2, url: 'http://rdvocab.info/ElementsGr2/'
```

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
