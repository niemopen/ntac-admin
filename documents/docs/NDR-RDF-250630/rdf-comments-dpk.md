# NDR RDF Comments

Section 14 of [NDR Version 6](https://github.com/niemopen/niem-naming-design-rules/blob/dev/ndr6src.md),
"Interpretation of NIEM data"

## Terminology

The Interpretation section begins:

> The primary purpose of a NIEM-based data specification is to establish a common understanding
> among developers, so that they can write software that correctly handles the shared data.
> Much of that common understanding comes from the natural language documentation in a NIEM model.
> For example, consider the documentation of the nc:PersonName **object** property shown below:

In natural language "object" means "thing", any discrete item that can be counted.
Baseballs and people are objects, as opposed to water which (above the molecular level) is a substance, not an item.

In computing, object has a more specific meaning: an item with associated behavior.
In natural language the number 5 is an object, but in computing the number 5 is an object with associated
operations (addition, coercion, mutability, etc.) as defined by a programming language while being processed,
along with a literal value (e.g., ASCII "5" or binary 0101) when stored or transmitted.
The literal value of a message or document has no operations or behavior, it just exists as static data.

NIEM uses a third meaning of object that conflicts with the first two:
some items are objects while other items are not.  In "the nc:PersonName object property" quoted above a
Person is a thing that has a Name which is also a thing, but NIEM categorizes Person as an "object" but
Name as "not an object".  "The nc:PersonName property" (without the object) communicates the identical
idea to developers without implying an artificial distinction between "object" and "non-object" properties.

The first example is:  
`<ObjectProperty structures:id="nc.PersonName"> | <xs:element name="PersonName" type="nc:PersonNameType">`
categorizing the same PersonName as an "ObjectProperty" for XML but an instance of PersonNameType" for JSON.

The word "object" appears 554 times in NDR6, but it appears only 10 times total over XSD volumes 1 and 2,
all in the natural language sense.  Eliminating the 100+ instances in section 14, removing it
when used as an adjective and replacing it with "map" type, value, or literal as appropriate when used as a noun,
would demonstrate feasibility of resolving these conflicting meanings.  The exception is JSON "object literals",
not objects, as defined by the Javascript and JSON specifications.

Given that there are two popular yet incompatible meanings of "object" it is probably unrealistic to eliminate
one meaning or another. But given that incompatible usages of object:
* **thing** - natural language term for any discrete physical or data entity
* **value** - logical and literal instance of Type, including all content
* **map** - logical and literal key:value instance of Class, excluding simple and list content (instance of Type)
* **object** - logical value of any data entity while being processed
* **literal** - lexical value of any data entity represented in a document, message, or string

are concepts that NIEM must express clearly, a Venn diagram and some approach to disambiguation
should be agreed.
* Is value always an instance of Type and never an instance of Class?
* Is object always an instance of Class and never an instance of Type?

**Example**:
```
Coordinate (1 dimension - latitude)               -- value or object?  "38.8895"
Coordinate (2 dimensions - lat, long)             -- value or object?  "38.8895,-77.0352"
Coordinate (3 dimensions - lat, long, altitude)   -- value or object?  "38.8895,-77.0352, 429.5"
```

## RDF
In the RDF section NDR says:
> The information in a NIEM message is expressed as values of properties of objects.
> * An object in NIEM XML is an element that has attributes, or complex content, or both.
> * An object in NIEM JSON is a JSON object that is the value of a key/value pair.

But in RDF "object" has its natural language meaning:
* In a natural language sentence, an object is typically a noun or pronoun that receives the action of the verb,
  or is the target of a preposition.
* In an RDF triple, the object is related to the subject by the predicate, but
  is not restricted to having attributes or complex content other than those created by other triples.
  Objects can be IRIs, blank nodes, or datatyped literals, none of which are complex.

This could be replaced with:
```
The information in NIEM is defined as typed values and expressed in messages as literals in a data format.
 * A NIEM type containing multiple values can be expressed in XML as an element with attributes, contained elements,
   and/or compound text.
 * A NIEM type containing multiple values can be expressed in JSON as an object (map) literal, an array literal,
   and/or a compound string.
```
Section 14.2.1 bullet 3 says:
```
A value in NIEM XML can be a literal, the value of an attribute or the simple content of an element.
A value in NIEM XML can also be an object, a child element with attributes and/or complex content.
A value in NIEM JSON can be an object, or a literal; that is, a string, number, or boolean.
(A value in NIEM JSON can also be an array of values for a repeatable property; see section TODO.)
```
which could be replaced with:
```
A NIEM value may be represented by an XML literal. The XML literal may represent the value as an element
with attributes, an element with simple content, or an element with child elements with attributes,
simple content, or complex content.

A NIEM value may be represented by a JSON literal. JSON literals are null, boolean, string, number, array
and object. An array or object may represent repeatable values of the same type or values with individually
defined types.
```

**Example:**  
Repeating the coordinate example, the information in a specific NIEM geographic coordinate can be expressed
as a message in various data formats:
```
XML:
    <Coordinate latitude="38.8895" longitude="-77.0352"></Coordinate>
    
    <Coordinate>
      <Latitude>38.8895</Latitude>
      <Longitude>-77.0352</Longitude>
    </Coordinate
    
    <Coordinate>38.8895, -77.0352</Coordinate>

JSON:
    {"latitude": 38.8895, "longitude": -77.0352}
    
    [38.8895, -77.0352]
    
    "38.8895, -77.0352"
```
**Example: Coordinate message in XML and JSON syntaxes**

As illustrated in the Coordinate example, XML elements normally use type QNames as tags but in JSON, type QNames
are carried in the JSON Schema, not in message data. Validating a message against its schema associates the type with
each value in a message, making it unnecessary to repeatedly transmit type names in each instance of that type.

In NDR Example 3-2, while the XML syntax is familiar to developers the JSON syntax would be a foreign language.
The following message example would be typical JSON, validated using JSON Schema with no JSON-LD processing.
* Only the message schema needs to be included in the message; the message schema identifies all other schema
namespaces for types used in the message, and can include metadata such as schema
description, software license, message specification, format identifiers, etc.
* JSON Schema does not support namespace prefixes because types do not appear in messages.
An information preprocessor can convert all QNames used in JSON Schema to absolute URIs.
* A more complete use case might define Message as having Request, Response, EventNotification,
and perhaps other message types, using QNames to identify types in both XML and JSON and locally-unique
properties within a type as shown in
[Self-Describing Messages](https://github.com/niemopen/ntac-admin/blob/main/documents/docs/SelfDescribing-250616.md).
* [Google's perspective](https://www.seobility.net/en/wiki/JSON-LD) is that "The main difference between
JSON and JSON-LD is JSON-LD’s implementation of [Schema.org](https://schema.org/)." This has nothing to do with
ontology definition or knowledge graph processing, it simply borrows a JSON-LD keyword
["@context"](https://github.com/schemaorg/schemaorg/blob/main/data/releases/29.2/schemaorg-all-https.jsonld)
to hold a list of namespace prefixes and "@type" to reference definitions from the schema into message data,
and suggests referencing types defined in a community schema, analogous to referencing types defined in the
NIEM core and domain schemas. This reuse of two loanwords does not require applications to perform any ontology
or knowledge graph (@graph) operations or use any RDF libraries to process messages, so the semantics of RDF
do not apply to NIEM messaging. The NIEM community, through NBAC, should have an opinion on whether to expand
the NIEM charter from reusable message design to ontology predicate and graph definition.

JSON Schema:
```
{
  "$id": "http://example.com/ReqRes/1.0/",
  "$ref": "#/$defs/Message",
  "$defs": {
    "Message": {
      "type": "object",
      "properties": {
        "schema": {"type": "string"},
        "req": {"$ref": "#/$defs/Request"}
      },
      "additionalProperties": false
    },
    "Request": {
      "type": "object",
      "properties": {
        "request_id": {"type": "string"},
        "item": {"$ref": "https://docs.oasis-open.org/niemopen/ns/model/niem-core/$defs/ItemName"},
        "quantity": {"$ref": "https://docs.oasis-open.org/niemopen/ns/model/niem-core/$defs/ItemQuantity"}
      },
      "additionalProperties": false
    }
  }
}
```
JSON Message:
```
{
  "schema": "http://example.com/ReqRes/1.0/",
  "req": {
    "request_id": "RQ001",
    "item": "Wrench",
    "quantity": 10
  }
}
```
**NDR Example 3-2: Schema and Message in JSON syntax**

**Model terminology:**
Table 14-3 compares terminology for CMF, XSD, and RDF.  In XSD the word "Class" appears only 10 times,
all in the natural language context: "class of XML documents", "classes of conforming processors", etc.

## Appendix: Objects and Programming Environments

All messages and documents, regardless of content, are **literals** -- immutable sequences of bytes or characters.

[XSD](https://www.w3.org/TR/xmlschema11-2/#datatype) defines datatype as having three properties:
* A value space, which is a set of values.
* A lexical space, which is a set of literals used to denote the values.
* A mapping from the lexical space into the value space, along with a few associated
functions, relations, and procedures.

Common usage across computing environments indicates that object vs. non-object reflects XSD's distinction
between internal logical values and external message/document literals, not a distinction between simple
and complex content.
Every value, either simple and complex, has both a lexical representation and internal program state.

**XML Objects**
>
> An XML object, in the context of XML processing, represents an element within an XML document.
> It's a fundamental part of the XML Document Object Model (DOM), which treats XML documents
> as trees of interconnected nodes. The XML object acts as an interface for accessing and
> manipulating the data and structure of the XML document.
> 
> **XML Object as a Node**:  
> The XML object represents a specific node in this tree.
> It provides access to the node's properties (like its name, value, and attributes)
> and methods for interacting with it (like adding, removing, or modifying child nodes).

**[RDF Objects](https://www.w3.org/TR/rdf12-concepts/)**
> The Resource Description Framework (RDF) is a framework for representing information on the Web.
> This document defines an abstract syntax (a data model) which serves to link all RDF-based
> languages and specifications. The abstract syntax has two key data structures:
> * RDF graphs are sets of subject-predicate-**object** triples, where the elements may be **IRIs,
> blank nodes, datatyped literals**, or triple terms. They are used to express descriptions of resources.
> * RDF datasets are used to organize collections of RDF graphs, and consist of a default graph
> and zero or more named graphs.


**[JavaScript Objects](https://www.w3schools.com/js/js_objects.asp)**
>
> **Objects** are containers for **Properties** and **Methods**.  
> **Properties** are named **Values**.  
> **Methods** are **Functions** stored as **Properties**.  
> **Properties** can be primitive values, functions, or even other objects.
>
> In JavaScript, almost "everything" is an object.
> * Objects are objects
> * Maths are objects
> * Functions are objects
> * Dates are objects
> * Arrays are objects
> * Maps are objects
> * Sets are objects
> 
> All JavaScript values, except primitives, are objects.

**Python Objects**
> In Python, all values, including primitives, are objects.
>
> In the Python statement `x = 5`:  
> 5 is an integer object, and x is a variable that refers to this integer object.
> x does not contain the value 5; instead, it holds a reference (or memory address) to the object 5.
> This distinction is important because:
> 
> **Identity**:  
> Multiple variables can refer to the same object.
> For example, if you assign y = x, both x and y will refer to the same integer object 5.
> 
> **Mutability**:
> When you modify a mutable object (like a list or dictionary) through one variable,
> the changes are reflected when accessing the object through any other variable referring to it.
> Immutable objects (like numbers, strings, and tuples) create new objects upon modification.

**[JSON Object Literals](https://www.w3schools.com/js/js_json_objects.asp)**
>
> This is a JSON string: `'{"name":"John", "age":30, "car":null}'`
>
> Inside the JSON string there is a JSON object literal: `{"name":"John", "age":30, "car":null}`
>
> JSON object literals are surrounded by curly braces {}.  
> JSON object literals contains key/value pairs.  
> Keys and values are separated by a colon.  
> Each key/value pair is separated by a comma.  
>
> ==========  
> It is a common mistake to call a JSON object literal "a JSON object".  
> JSON cannot be an object. JSON is a string format. The data is only JSON when it is in a string format.  
> When it is converted to a JavaScript variable, it becomes a JavaScript object.  
> ==========  

**[ECMAScript](https://tc39.es/ecma262/#sec-ecmascript-data-types-and-values) Objects**
>
> **6 ECMAScript Data Types and Values**
> 
> Algorithms within this specification manipulate values each of which has an associated type.
>  
>  ...  
> **6.1.4 The String Type**   
> **6.1.6 Numeric Types**   
> **6.1.7 The Object Type**  

This confusingly uses the term "object" as one of its "Data Types and Values".
It conflates the "objects" of all types that are manipulated by algorithms in the language with the specific
"object" key/value type that in other programming languages is called "associative array", "hash", "dictionary",
or "map".  But it does confirm that the "object" key/value type in JavaScript/ECMAScript is a data type
that has both an algorithmic internal value and a literal representation.


## References

###### [XSD1.1 Part 1 - Structures](https://www.w3.org/TR/xmlschema11-1/)
###### [XSD1.1 Part 2 - Datatypes](https://www.w3.org/TR/xmlschema11-2/)