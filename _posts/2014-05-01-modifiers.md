---
layout: page
title: "Modifiers"
category: manager
date: 2014-05-01 16:11:14
---

Modifiers are methods that provide an easy way to accomplish common tasks to manipulate or retrieve data in specific ways.

The modifiers below can be used in the attribute definition blocks.

### Retrieve Value

There are two ways to extract data in order to manipulate it for a specific attribute. Both ways then allow to chain other modifiers to actually make the changes to the data.

#### Get

The **get** method retrieves the value of a attribute that has already been processed. (The attributes are always processed from top to bottom).

```ruby
attribute :subjects, xpath: "//subject"

attribute :something_else do
  get(:subjects)
end
```

#### Fetch

The **fetch** method is provided to have access to the the raw data.

##### XML based strategy (XML, RSS)
```ruby
attribute :creator do
  fetch("//author")
end
```

##### JSON based strategy (JSON)
```ruby
attribute :creator do
  fetch("author")
end
```

### Convinience Methods
The following set of methods allow the operator to inspect the values of a particular field. These methods are specially usefull when the operator needs to acomplish conditional 

#### present?
Checks if there is any value at all. Returns true or false.

```ruby
attribute :category, xpath: "//category" do
  category = get(:category)
  category += ["Images"] if get(:thumbnail_url).present?
  category
end
```

#### includes?
Checks if the value provided is included in the set.

```ruby
attribute :category, xpath: "//category" do
  category = get(:category)
  category += ["News"] if get(:dc_type).includes?("News")
  category
end
```

#### to_a
Converts the AttributeValue object to a regular array.
```ruby
attribute :category, xpath: "//category" do
  get(:category).to_a
end
```

#### first
It returns the first value

```ruby
attribute :category, xpath: "//category" do
  get(:category).first
end
```

### Modify Value

#### find_with(regexp)
It returns the first element that matches the regular expression.

```ruby
attribute :identifier do
  get(:identifier).find_with(/IE:\w/)
end
```

#### find_all_with(regexp)
It returns all the elements that matches the regular expression.

```ruby
attribute :identifier do
  get(:identifier).find_all_with(/IE:\w/)
end
```

#### find_without(regexp)
It returns the first element that doesn't match the regular expression.

```ruby
attribute :identifier do
  get(:identifier).find_without(/http/)
end
```

#### find_all_without(regexp)
It returns all the elements that don't match the regular expression.

```ruby
attribute :identifier do
  get(:identifier).find_all_without(/http/)
end
```

#### mapping(regexp, substitute_value)
It replaces all the values with the regular expression specified

```ruby
attribute :enrichment_url do
  get(:identifier).mapping(/.*handle.net(.*)/ => 'https://researchspace.auckland.ac.nz/handle\\1?show=full')
end
```

The mapping key value pairs can also be repeated to perform multiple substitutions on the same value. For exmaple:

```ruby
attribute :enrichment_url do
  get(:identifier).mapping(/width=[\d]{1,4}/ => 'width=520', /height=[\d]{1,4}/ => 'height=310')
end
```

To use captured values from the regular expression use \1 for the first captured value \2 for the second on so on.

#### select(start, end)
It allows you to select any element or range of elements within an array

##### Select the first element
```ruby
attribute :creator do
  get(:creator).select(:first)
end
```

##### Select the last element
```ruby
attribute :creator do
  get(:creator).select(:last)
end
```

##### Select from the 2nd element to the last
```ruby
attribute :creator do
  get(:creator).select(2, :last)
end
```

##### Select from the first element to the second to last
```ruby
attribute :creator do
  get(:creator).select(:first, -2)
end
```

#### add(value)
It appends a value to a attribute.

```ruby
attribute :category, default: ["Newspapers"] do
  get(:category).add("Images") if get(:thumbnail_url).present?
end
```

#### compose(value1, value2...)
It joins multiple values from potentially different places.

```ruby
attribute :subjects do
  compose(get(:tag), "New Zealand", get(:title))
end
```

#### to_date
It tries to parse the values into real dates.

```ruby
attribute :date do
  get(:display_date).to_date
end
```

#### split(split_value)
It will try and split all values by the value specified.

```ruby
attribute :subject do
  get(:subject).split(",")
end
```

The above code will convert a string in the following format "dogs, cats, puma" to ["dogs", " cats", " puma"]

```ruby
attribute :subject do
  get(:subject).split(/\d/)
end
```

The splitter can also split based on a regular expression.

#### truncate(length, omission="...")
It will truncate all values to the specified length. The omission defaults to three dots "...", but a different string can be specified.

```ruby
attribute :description do
  get(:description).truncate(300)
end
```

It will truncate the string to 300 charachters.

```ruby
attribute :description do
  get(:description).truncate(10, "")
end
```

It will truncate the string to 10 characters and it won't add anything at the end of the string.

#### downcase
It will downcase all the values

```ruby
attribute :identifier do
  get(:identifier).downcase
end
```
