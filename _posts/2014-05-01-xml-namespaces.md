---
layout: page
title: "XML Namespaces"
category: manager
date: 2014-05-01 16:12:04
order: 4
---

Many XML, RSS and OAI sources we harvest make use of XML Namespaces. Understanding namespaces and using them correctly is important to make parsers behave predictably.
Theres a lot of material online for a complete definition of what namespaces are an how to use them, this page describes some of the specifics that will make writing parsers easier.

### Namespace basics

* XML documents can contain elements (like a `<title>`) from different vocabularies. For example a `<title>` of a book may have a different meaning that a `<title>` of a person.
* Namespaces are used to make an element (like a `<title>`) specific by adding a unique identifier to them.
* The Namespace Name (the unique identifier) is normally a URI
* In XML files, namespaces can be specified using the `xmlns` attribute
* Some namespaces are defined with prefixes, and some are defined as default namespaces
* Namespaces described on an element apply for that element and all it's decendant elements. For example

```xml
<items xmlns="http://bookschema.com" xmlns:dc="http://purl.org/dc/elements/1.1/">
  <book>
    <title>Trail</title>
    <author xmlns="http://schema.com/person">
      <name>Sorrell, Paul</name>
      <title>Mr</title>
    </author>
    <dc:identifier>123</dc:identifier>
  </book>
</books>
```

In this example, there are two different default namespaces, and one namespace with a prefix:
* `<items>`, `<book>` and the `<title>` of the book belong to the vocabulary identified by `"http://bookschema.com"`
* `<author>`, `<name>` and the `<title>` of a person belong to the vocabulary identified by `"http://schema.com/person"`
* `<dc:identifier>` belongs to the vocabulary identified by `"http://purl.org/dc/elements/1.1/"`


### Namespaces and XPath

* Using XPath to select elements from a document requires being specific about which namespaces the elements you are looking for belong to. For example, asking for the XPath `//title` in the document above could match either the title of a book or the title of a person. 
* In an XPath expression, default namespaces (ones that have no prefix) are ambiguous, because different default namespaces can be applied to different parts of the document. For this reason all namespaces need a prefix in xpath
* Likewise, the prefixes in the document can also be ambiguous because the same prefix can be used to describe multiple namespaces in different parts of the same XML file. 
* prefixes themselves are only important in context, in XPath we can define our own prefixes to describe the namespaces we mean.
* For this reason, an XPath expression is accompanied by a definition of which prefix we want to use to select elements from a particular namespace.

Heres an example:

Given the XML above, we choose to define our namespaces for the XPath as:
```json
book: "http://bookschema.com",
dc: "http://purl.org/dc/elements/1.1/",
person: "http://schema.com/person"
```
Now we can use XPath experessions like:
* `//book:title` selects any titles of books
* `//person:title` selects any title of a person
* `/book:items/book:book/dc:identifier` selects the identifier within a book


### Namespaces in Squirrel Parsers

To make parsers readable, we want to use a consistant set of namespace prefixes for all the XPath in a parser. To make this easy, a harvest operator defines the namespaces and their prefixes in one place in the parser.

```ruby
namespaces book: "http://bookschema.com",
             dc: "http://purl.org/dc/elements/1.1/",
         person: "http://schema.com/person"
```

With the namespaces defined, all the xpath in the parser can make use of these prefixes.

```ruby
record_selector '/book:items/book:book'

attributes :title, xpath: '/book:title'
attributes :author, xpath: '/person:author/person:name'
attributes :identifier do 
  fetch('/dc:identifier').first
end
```

### Tips

* To make parsers as strightfoward as possible, it is advisable to use the same prefixes as the XML file, unless the prefix is ambiguous (is used for two different namespaces). For example using 'dc' as the prefix for the dublin core schema ('http://purl.org/dc/elements/1.1/') is common in almost every xml file using this namespace. 
* Adding common namespaces to parser templates is a good way to reduce the amount of work to create new parsers. For example most OAI feeds use the 'http://purl.org/dc/elements/1.1/' namespace.




