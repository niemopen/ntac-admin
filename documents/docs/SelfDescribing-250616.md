# Self-describing messages

Every NIEM message should contain a reference to its specification, so that anyone who has a message will be able to obtain the definitions of the components in that message. That is what we mean when we say that NIEM data is self-describing.  How to arrange this is the topic of this paper. ([*NIEM Message Specifications (2023)*](https://github.com/niemopen/ntac-admin/blob/main/documents/docs/N6MsgSpec-220802.docx) also talks about this topic.)

It might be helpful to review the terms in [NDR 6.0, section 3.1](https://niemopen.github.io/niem-naming-design-rules/ndr-v6.0-psd01.html#31-machine-to-machine-data-specifications):

* message:  a package of data shared at runtime; an instance of a message format and message type
* message format: a definition of a syntax for the messages of a message type
* message type: a definition of the information content in equivalent message formats
* message specification: a collection of related message types

A single message type may have more than one message format. The message examples below illustrate one message type, and a total of four message formats of that type:

* a canonical XML format, with component names from the model, like `nc:ItemName`
* a canonical JSON format
* a simple XML format, with developer-friendly names, like `iname`
* a simple JSON format, with developer-friendly names

Messages are convertible between the formats of a message type. The four example messages below have the same information content. Each can be converted into any of the others without loss. Below are examples of the two canonical message formats, equivalent to each other. (Strings may be truncated and closing tags omitted in all examples.)

```
<msg:Request                                                  | {
 xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem |   "@context": {
 xmlns:msg="http://example.com/ReqRes/1.0/">                  |     "nc": "https://docs.oasis-open.org/niemopen/ns/model/niem
  <msg:RequestID>RQ001</msg:RequestID>                        |     "msg": "http://example.com/ReqRes/1.0/"
  <msg:RequestedItem>                                         |   },
    <nc:ItemName>Wrench</nc:ItemName>                         |   "msg:Request": {
    <nc:ItemQuantity>10</nc:ItemQuantity>                     |     "msg:RequestID" : "RQ001",
  </msg:RequestedItem>                                        |     "msg:RequestedItem": {
</msg:Request>                                                |       "nc:ItemName": Wrench",
                                                              |       "nc:ItemQuantity": 10
                                                              |      }
```
<figcaption><a name="ex1">Example 1: Canonical XML and JSON messages</a></figcaption>

The data structure of a NIEM message is a directed graph with an initial node, known as the *message object*.  The message object is the value of a property, known as the *message property*.  A message type declares a single message property.  In the examples above, the URI of the message property is `http://example.com/ReqRes/1.0/Request`.

Here are examples of two simple message formats, equivalent to each other and to the above examples.

```
<request xmlns="http://example.com/ReqRes/1.0/simpleXML">     | {
  <id>RQ001</id>                                              |   "@context": "http://example.com/ReqRes/1.0/simpleJSON",
  <item>                                                      |   "msg": {
    <iname>Wrench</iname>                                     |     "id": "RQ001",
    <count>10</count>                                         |     "item": {
  </item>                                                     |       "name": "Wrench",
</request>                                                    |       "number": 10
                                                              |     }
```
<figcaption><a name="ex2">Example 2: Simplified XML and JSON messages</a></figcaption>

## 1. Making data self-describing

Data is self-describing when it contains the means to identify (and perhaps also locate and retrieve) the resources needed to understand its meaning.  NIEM 3.0 introduced much of what we need for that, with its mapping from the QName of an XML element or attribute to the URI for the definition of that message component.

We then chose to use JSON-LD for NIEM JSON messages.  The `@context` object in JSON-LD also provides a mapping from a compact IRI to the URI for the definition of that message component.  (A compact IRI is just like a QName for our purposes.)

For example, `nc:ItemName` maps to `https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/ItemName` in the canonical XML message and in the canonical JSON message above.

So far, so good; however, in addition to the mapping for individual message model components, we want to have something in every message that can be interpreted as an identifier for:

* the message format, to understand how the message can be validated
* the message type, to understand what the message can contain and what that content means
* the message specification, to understand where to look for the specification artifacts

The rest of this paper explores the conventions that will allow us to find those identifiers in any NIEM message.  (We could turn these into rules, later on, if we write another specification.)

## 2. Identifiers in a canonical message format

Given a canonical message in XML or JSON, what do we have to work with?  Well, we can always find the message property: it is the root element in an XML message, or the value of the root key in a JSON message.  So we always know the URI of the message property, and the URI of its namespace.  The following conventions will then give us the identifiers we want:

1. Let's say that all of the message properties in a message specification must be defined in a single namespace.  Let's say that the URI of that namespace MUST end in a `/` character.  Let's say that the URI of the message specification is the URI of that namespace.  Then a canonical message always provides the namespace specification URI; it is the namespace of the message property.  In [example 1](#ex1), that URI is `http://example.com/ReqRes/1.0/`.

2. Let's say that the URI of a message type is the URI of its message property. In [example 1](#ex1), that URI is `http://example.com/ReqRes/1.0/Request`.

3. Let's say that the URI of a canonical message format is the same as the URI of its message type.  That means the canonical XML and JSON message formats have the same URI.  That's OK, everyone will know which one they need.

That takes care of canonical XML messages, and of canonical JSON messages with an inline context.

## 3. Identifiers for a canonical JSON message with a context reference

A valid NIEM JSON message must have a `@context` object, but this does not have to be an inline context, such as the one shown in [example 1](#ex1) above.  You can instead provide the context resource separately, and then reference that context in the message by its URI.  This is illustrated by [example 4](#ex4) below.  In this sort of JSON message, we will always have the context resource URI, and we will have the QName of the message property.  The following conventions will then give us the identifers we want:

1. Let's say that every message specification with a JSON format must contain a context resource with mappings for all the namespaces in all the message types. 

2. Let's also say that the URI of that resource is the message specification URI, with "@context" appended.  For the message type illustrated in [example 1](#ex1), that context resource would contain:

```
{
  "@context": {
    "nc": "https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/",
    "msg": "http://example.com/ReqRes/1.0/"
  }
```
<figcaption><a name="ex3">Example 3: Context resource for a message type</a></figcaption>

And then we could write the JSON message in [example 1](#ex1) like this:

```
{
  "@context": "http://example.com/ReqRes/1.0/@context", 
  "msg:Request": {
    "msg:RequestID" : "RQ001",
    "msg:RequestedItem": {
      "nc:ItemName": Wrench",
      "nc:ItemQuantity": 10
```
<figcaption><a name="ex4">Example 4: Canonical JSON message with a context reference</a></figcaption>

Given a JSON message with a context reference, like the one in [example 4](#ex4) above, how do we get the message specification, type, and format URIs?

1. We get the URI of the message specification by removing the `@context` suffix from the context reference.  This will turn out to be the URI of the message property namespace.  In our example, that is `http://example.com/ReqRes/1.0/`.

2. We get the URI of the message type by expanding the message property's compact IRI.  In our example, that expansion produces `http://example.com/ReqRes/1.0/Request`.

3. Since this is a canonical message format, its URI is the same as the URI of the message type.

And so now we have conventions to give us the needed identifiers for all canonical messages, whether or not they have an inline context.

## 4. Identifiers for a JSON message with simple keys

JSON developers often prefer simple keys like "item" instead of NIEM-conforming names like "nc:ItemName".  We can support that in a simple message format, by allowing the message designer to provide a special context resource.  For the simple JSON message format illustrated in [example 2](#ex2) (and reproduced here), one would write the following context:

```
{                                                             | {
  "@context": {                                               |   "@context": "http://example.com/ReqRes/1.0/simpleJSON",
    "nc": "https://docs.oasis-open.org/niemopen/ns/model/     |   "msg": {
    "msg": "http://example.com/ReqRes/1.0/",                  |     "id": "RQ001",
    "request": "msg:Request",                                 |     "item": {
    "id": "msg:RequestID",                                    |       "name": "Wrench",
    "item": "msg:RequestedItem",                              |       "number": 10
    "name": "nc:ItemName",                                    |     }
    "number": "nc:ItemQuantity                                |   }
  }                                                           | 
```
<figcaption><a name="ex5">Example 5: Context resource and example message for a simple JSON message format</a></figcaption>

The compact IRIs in the message on the right all resolve to the canonical NIEM URIs, given the context on the left.  The following conventions will give us the identifiers we need for simple JSON messages:

1. Let's say that a simple JSON message format has a name that begins with a lower-case character; for example, "simpleJSON".

2. Let's say that you get the URI of a simple JSON message format by appending that name to the message specification URI; for example, `http://example.com/ReqRes/1.0/simpleJSON`.

3. Let's say that the URI of a simple JSON message format and the URI of its context resource are the same.  

Given the context resource and simple JSON message in [example 5](#ex5) above, then:

1. The context reference in the message is `http://example.com/ReqRes/1.0/simpleJSON`.  We get the URI of the message specification by removing everything after the last `/`.

2. The message property is `msg`.  When we expand that compact IRI using the context resource, we get `http://example.com/ReqRes/1.0/Request`.  That is the URI of the message type.

3. The context reference in the message is also the URI of the message format.

Now we have conventions to give us the needed identifiers for the instances of a simple JSON message format.

## 5. Identifiers for a simple XML message

If NIEM JSON developers get to work with simple messages, then NIEM XML developers should be able to do likewise.  [Simplified NIEM XML (2021)](https://github.com/niemopen/ntac-admin/blob/main/documents/docs/SimpleNIEM-211115.docx) describes some common developer complaints about NIEM XML and proposes several responses.  One of these responses is a simple XML message format in which the canonical element and attribute names are replaced with simple names *all in the same namespace*.  [Example 2](#ex2) contains an example of such a message.

XML has nothing like the JSON-LD context for mapping one component name to another, so we'll have to pick something for simple XML message formats.  Best I have right now is RDF+OWL.  Then the mapping for the simple XML format illustrated in [example 2](#ex2) looks like this:

```
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix msg: <http://example.com/ReqRes/1.0/> .
@prefix nc: <https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/> .
@prefix simple: <http://example.com/ReqRes/1.0/simpleXML/> .
simple:request owl:sameAs msg:Request .
simple:id owl:sameAs msg:RequestID .
simple:item owl:sameAs msg:RequestedItem .
simple:iname owl:sameAs nc:ItemName .
simple:count owl:sameAs nc:ItemQuantity .
```
<figcaption><a name="ex6">Example 6: Mapping file for a simple XML message format</a></figcaption>

There are plenty of tools that will transform the simple XML message in [example 2](#ex2) into the canonical XML message in [example 1](#ex1), based on the mappings defined above.  Our problem is to find a mapping specification they will all accept, or to choose some of those mapping specifications and write our own tool to generate those.  That's a problem for later.  For now, assume a solution, perhaps the one shown above.  Then the following conventions will give us the identifiers we need:

1. Let's say that a simple XML message format also has a name that begins with a lower-case character; for example, "simpleJSON".

2. Let's say that you get the URI of a simple XML message format by appending that name to the message specification URI; for example, http://example.com/ReqRes/1.0/simpleXML.

3. Let's say that the URI of the simple XML mapping file and the URI of the simple message format are the same.  For this example, let's say that URI is `http://example.com/ReqRes/1.0/simpleXML".

```
<request
  xmlns="http://example.com/ReqRes/1.0/simpleXML"> 
  <id>RQ001</id>  
  <item> 
    <iname>Wrench</iname>  
    <count>10</count>
  </item> 
</request>
```

Then, given the message in [example 2](#ex2) (reproduced above):

1. The namespace of the message property is `http://example.com/ReqRes/1.0/simpleXML`.  We get the URI of the message specification by removing everything after the final `/`.

2. The message property is `request`.  According to the NIEM rules for assigning URIs to QNames, its URI is `http://example.com/ReqRes/1.0/simpleXML/request`.  When we transform that URI using the mappings in [example 6](#ex6), we get `http://example.com/ReqRes/1.0/Request`. That is the URI of the message type.

3. The namespace of the message message property is the URI of the message format, `http://example.com/ReqRes/1.0/simpleXML`.

Now we have the conventions to give us the needed identifiers for the instances of a simple XML message format.  That means we pass GO and collect $200!

## 6. Conventions for identifiers of message specifications, types, and formats

Let's gather these in one place.  (I wrote them like rules, with MUSTs and SHOULDs, but of course it's easy to convert those to lower case.)

1. All of the message properties in a message specification MUST be defined in the same namespace.  The URI of that namespace is the URI of the message specification.  It MUST end in a `/` character.  It SHOULD end in the pattern `/`*version-identifier*`/`.  The version identifer SHOULD conform to the Semantic Versioning specification.  (See NDR rules 8-3, 8-4, 8-5.)

2. A message specification that contains JSON message formats MUST contain a context resource that defines all of the namespaces used in all of the message formats.  The URI of this resource is the URI of the message specification with `@context` appended.

3. The URI of a message type is the URI of its message property.

4. The name of a simple JSON message format or a simple XML message format MUST be a NCName that begins with a lower-case character.

5. The URI of a simple JSON message format or a simple XML message format is the URI of the message specification with the format name appended.

6. The URI of the context resource for a simple JSON message format is the same as the URI for the format.  The context resource MUST map every key in every message to a component in the model for the message type.

7. A simple XML message format MUST have a mapping from every element and attribute in every message to a component in the model for the message type.  The URI for this mapping resource is the URI of the message format.

## 7. Interpreting message URIs

Finally, let's go over the algorithm for extracting message specification, type, and format URIs from a NIEM XML or JSON message.

### 7.1 XML message

1. The message property is the root element
2. The namespace of the message property, minus the suffix following the final `/` character (if any), is the message specification URI.
3. If no suffix, then this is a canonical XML message\
    3.1 The URI of the message property is the URI of the message type and the message format
4. If the suffix is present, then this is a simple XML message\
    4.1 The suffix is the name of the simple message format\
    4.2 The namespace of the message property is the URI of the message format and of the format's mapping resource\
    4.3 The URI of the message property, once translated according to the mapping resource, is the URI of the message type.

### 7.2 JSON message with inline context

1. The message property is the compact IRI in the outermost JSON object that is not `@context`
2. The URI produced by expanding the message property using the inline context is the URI of the message type
3. The URI of the message type, minus the suffix following the final `/` character, is the URI of the message specification
4. The URI of the message format is the same as the URI of the message type

### 7.3 JSON message with a context reference

1. The message property is the compact IRI in the outermost JSON object that is not `@context`
2. The value of the `@context` key in the outermost JSON object, minus the suffix following the final `/` character, is the message specification URI
3. If the suffix is `@context`, then this is a canonical JSON message\
    3.1 The URI produced by expanding the message property using the context reference is the URI of the message type\
    3.2 The URI of the message format is the same as the URI of the message type
4. If the suffix is not `@context`, then this is a simple JSON message\
    4.1 The suffix is the name of the simple message format\
    4.2 The URI of the context reference is the URI of the message format\
    4.3 The URI produced by expanding the message property using the context reference is the URI of the message type


Author: Scott Renner\
Date: 2025-06-16

<style>
h1 { font-size: 14pt; }
h2,h3,h4 { font-size: 12pt;  }
code { font-family: "Source Code Pro", "Liberation Mono", monospace; font-size: 11pt; }
pre { background-color:#f0f0f0; padding: 6px; page-break-after: avoid; }
pre > code { font-size: 9pt; margin-left:auto; margin-right:auto; page-break-after: avoid; }
figcaption { text-align:center; font-style:italic; margin-top: 10pt; margin-bottom:10pt;  page-break-before: avoid; }
body { font-family: LiberationSans, Arial, Helvetica, sans-serif; font-size: 12pt; line-height: 1.2; }
</style>