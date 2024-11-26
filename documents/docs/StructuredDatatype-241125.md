# Literal properties or structured datatype?

This issue concerns the modeling of XML elements that have a type with simple content and attributes, and the representation of those elements in JSON and RDF.  For example:

```
<nc:PersonName>
  <nc:PersonMiddleName>Alice</nc:PersonMiddleName>
</nc:PersonName>
<nc:PersonName>
  <nc:PersonMiddleName nc:partialIndicator="true">Bob</nc:PersonMiddleName>
</nc:PersonName>
```

In the NTAC discussion papers from the past two years, and in the current draft of NDR6, the JSON and RDF representations of those elements include literal properties:

```
"nc:PersonName": {                |  _:n0 rdf:type nc:PersonNameType .
  "nc:PersonMiddleName": {        |  _:n0 nc:PersonMiddleName _:n1 .
    "nc:TextLiteral": "Alice"     |  _:n1 rdf:type nc:PersonNameTextType .
  }                               |  _:n1 nc:TextLiteral "Alice".
}                                 | 
```

```
"nc:PersonName": {                    |  _:n0 rdf:type nc:PersonNameType .
  "nc:PersonMiddleName": {            |  _:n0 nc:PersonMiddleName _:n1 .
    "nc:TextLiteral": "Bob",          |  _:n1 rdf:type nc:PersonNameTextType .
    "nc:partialIndicator": "true"     |  _:n1 nc:TextLiteral "Bob".
  }                                   |  _:n1 nc:partialIndicator "true" .
}                                     | 
```

Those literal properties only appear when the complex type with simple content (CSC type) has model attributes. When those attributes are removed from the message model subset, the JSON and RDF representations become:

```
"nc:PersonName": {                    |  _:n0 rdf:type nc:PersonNameType .
  "nc:PersonMiddleName": "Alice"      |  _:n0 nc:PersonMiddleName "Alice" .
}                                     |
```

Most CSC types do not have model attributes, and I believe most message specifications omit attributes from CSC types in the model subset, so this simple case is also by far the common case.

**The "structured datatype" alternative**

Christina suggests an alternative employing `rdf:value`. Part of the proposal involves definition of terms. We would call:

* `nc:PersonMiddleName` a data property
* `nc:PersonNameTextType` a *structured datatype*
* `Bob` a *structured value*.

We would use the JSON and RDF representations from the NIEM 5 JSON specification. These would look like:

```
"nc:PersonName": {                    |  _:n0 rdf:type nc:PersonNameType .
  "nc:PersonMiddleName": Alice"       |  _:n0 nc:PersonMiddleName _:n1 .
}                                     |  _:n1 rdf:type nc:PersonNameTextType .
                                      |  _:n1 rdf:value "Alice".                                   
```

```
"nc:PersonName": {                    |  _:n0 rdf:type nc:PersonNameType .
  "nc:PersonMiddleName": {            |  _:n0 nc:PersonMiddleName _:n1 .
    "rdf:value": "Bob",               |  _:n1 rdf:type nc:PersonNameTextType .
    "nc:partialIndicator": "true"     |  _:n1 rdf:value "Bob".
  }                                   |  _:n1 nc:partialIndicator "true" .
}                                     | 
```

Benefits:

1. One fixed `rdf:value` property, instead of 30+ `FooLiteral` properties
2. No worries about errors in the CMF (`FooType` class must have `FooLiteral` data property)
3. Properties don't switch between ObjectProperty and DataProperty depending on subset.

**Dr. Scott's Evaluation**

I see a number of difficulties with this alternative approach.

*1. NIEM terminology would not match JSON or RDF terminology*

Our term "data property" won't make sense to JSON and RDF developers. The actual representation of the middle name "Alice" is an object in RDF. The actual representation of "Bob" is an object in both JSON and RDF. A property that has an object value is an object property. Sure, we can call it a data property, but no one else will.  So we will then require a lot of explaining to people who won't otherwise understand. 

If we want inferencing to work for NIEM data (and we do!), then we need to get the OWL right in the RDF version of the model. That means writing:

```
nc:PersonMiddleName rdf:type owl:ObjectProperty .
nc:PersonMiddleName rdf:range nc:PersonNameTextType .
```

Our term "structured datatype" won't make sense, either. An object property has a type that is a class, so the RDF for the model needs to say:

```
nc:PersonNameTextType rdf:type owl:Class .
```

*2. Reworking CMF so that a `Datatype` can have attributes is too hard*

We can imagine reworking CMF so that a `Datatype` element can represent a value with attributes. But we can't actually do that *and* publish CMF and NDR6 this year. So in the NIEM model, the CMF representation of each link in the `nc:PersonNameType` derivation chain has to be a `ClassType` element.

However, we *could* have atomic classes without literal properties. The CMF would then look like this:

```
<ClassType>
  <Name>TextType</Name>
  <HasValue structures:ref="xs:string"/>
  <HasProperty>
    <Property structures:ref="nc:partialIndicator"/>
```

That in fact is how Webb handled simple content with attributes in the original metamodel. But I replaced his `HasValue` element with a literal property, because...

*3. Literal properties are technology-neutral; `HasValue` is not*

XML is the only message serialization I see that has anything like attributes. JSON, YAML, Protobuf, Avro, RDF - none has an equivalent way to attach properties to literals. So building `HasValue` into CMF does two bad things:

1. It carries this XML-specific modeling concept into the future.
2. It means NIEM has two ways to do one thing; that is, model a value with properties.

Suppose you want to model a weight plus units of measure; e.g. "17 kilos". Pretty much everyone (including NIEM) models this as a class with two properties: a value property and a units property.  For example, given an element of `nc:WeightType`, you'll see

```
  <my:ThingWeight>
    <unece:MassUnitCode>KGM</unece:MassUnitCode>
    <nc:MeasureDecimalValue>22.5</nc:MeasureDecimalValue>
  </my:ThingWeight>
```

Do we really need a second way to model a value that has properties? For XML guys it may be convenient to use an attribute in the message, but why must we build this into our model format?

*4. `rdf:value` is not best practice*

It's in the RDFS specification in much the same way my large intestine has an appendix: not used for much.  I believe it's another idea that, like reification with `rdf:Statement`, did not really catch on.  I could be wrong, of course, but here's my evidence:

* I read the new edition of Hendler's *Semantic Web for the Working Ontologist*, and it doesn't mention `rdf:value`. (Hendler is my authority for semantic web and ontology; I worked with him on a USAF Scientific Advisory Board study back in the day.) But the book does have an example of a price measured in dollars, which looks like this:

   ```
   my:PriceSpec
     rdf:type my:UnitPriceSpecType ;
     my:price "15.0" ;
     my:priceCurrency "USD" .
   ```
   So my best current textbook for ontologies and inferencing is using a named property for an amount with units of measure. It's not using `rdf:type`.

* I searched StackOverflow in particular and the web in general; found basically nada.

* I asked ChatGPT.  "Its use is not particularly common or standardized in RDF and OWL applications... while rdf:value is available and can be used in RDF data, it is not commonly used in well-structured ontologies where more specific properties are defined."

*5. Processing NIEM JSON would require tests at runtime*

Under the structured datatype proposal, if `nc:PersonNameTextType` includes model attributes, then software processing a JSON message could encounter either of these structures:

```
"nc:PersonName": {                    |  "nc:PersonName": {
  "nc:PersonMiddleName": "Alice"      |    "nc:PersonMiddleName": {
}                                     |      "rdf:value": "Bob",
                                      |      "nc:partialIndicator": "true"
                                      |    }
                                      |  }           
```

When you need that middle name, you retrieve the value of the `nc:PersonMiddleName` key.  That value could be a string, in which case you're done. Or it could be an object, in which case you must then retrieve the value of the `rdf:value` key. Your code has to test and branch to handle either case.

Not so with literal properties. If `nc:PersonMiddleName` is a data property in the message model, then at runtime you'll always get the name string.  If it's an object property, you'll always get an object, from which you always extract `nc:TextLiteral`. No tests, no branching required. The message specification tells you what you will get.

*6. `rdf:value` mixes RDF into JSON*

I'm a JSON developer. 

* I have to retrieve the `nc:TextLiteral` key?  That property is in the message model. OK.
* I have to retrieve the `rdf:value` key?  Hey, why am I seeing RDF stuff in my data?

*Summary*

I still prefer literal properties over structured datatypes.

* Better fit to JSON and RDF terminology
* Better practice for ontologies and inferencing
* Better data technology independence
* Better JSON for developers of consuming software

**But this still feels weird**

> Conceptually, I'm still struggling with value properties jumping back and forth between data properties and objects based on whether or not attributes are in the subset. This seems like Schrodinger's property - it's not a data property or an object until you look inside its type. There's no way to tell otherwise.

Remember, in NIEM, *interoperability happens at the IEPD level*.  You always have to look at the message specification to fully understand the message. My system produces a NIEM message, and your system consumes NIEM messages - are we interoperable? For messages of the same type, yes. In general, no. 

A property is always completely defined in its message model. It's always a data property, or always an object property, *in that message model*.  You do have to look at the message model to see which it is.  But having to look at the message model is not a strange new thing for NIEM.

\
Author: Scott Renner\
Last modified: 2024-11-26

<style>
    h1,h2,h3 { font-size:14pt }
    pre { background-color:#f0f0f0; }
    code { background-color:#f0f0f0; }
    span.emp { font-size:10pt; font-family: Arial, sans-serif; font-style:italic; }
</style>