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
all of them in a natural language sense.  Eliminating the 100+ instances in section 14, removing it
when used as an adjective and replacing it with type or value as appropriate when used as a noun,
would demonstrate feasibility of resolving these conflicting meanings.

**Example**:
```
Coordinate (1 dimension - latitude, or hwy mile marker) -- Value or Object?  "38.8895"
Coordinate (2 dimensions - lat, long)                   -- Value or Object?  "38.8895,-77.0352"
Coordinate (3 dimensions - lat, long, altitude)         -- Value or Object?  "38.8895,-77.0352, 429.5"
```

## RDF


## Appendix: Objects and Programming Environments

All messages and documents, regardless of content, are literals -- immutable sequences of bytes or characters.

[XSD](https://www.w3.org/TR/xmlschema11-2/#datatype) defines datatype as having three properties:
* A value space, which is a set of values.
* A lexical space, which is a set of literals used to denote the values.
* A mapping from the lexical space into the value space, along with a few 
functions, relations, and procedures associated with the datatype.

Common usage across computing environments indicates that object vs. non-object reflects XSD's distinction
between internal logical values and external message/document literals, not a distinction between simple
and complex content.
Any value, either simple and complex, has both a lexical representation and internal program state.

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


## References

###### [XSD1.1 Part 1 - Structures](https://www.w3.org/TR/xmlschema11-1/)
###### [XSD1.1 Part 2 - Datatypes](https://www.w3.org/TR/xmlschema11-2/)