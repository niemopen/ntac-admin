## Attribute Augmentations and IC-ISM in NIEM 6

Our objective is a technology-neutral NIEM 6.  We intend to support automated conversion of a NIEM message among equivalent serializations in XML, JSON, etc.  We intend to convert a NIEM message model in CMF into developer artifacts useful to developers using those serializatioins:  XSD, JSON Schema, etc.  It's important to avoid strange and ugly artifacts and serializations; NIEM XML should seem natural to XML developers, NIEM JSON  to JSON developers, etc.  Towards this last goal we have worked hard to avoid the *ugly JSON problem* in NIEM 6. 

In NIEM 3.0 we implemented the *Information Security Marking (ISM)* specification from the US Intelligence Community (IC).  That implementation causes ugly JSON.  This paper describes the problem and proposes a solution for NIEM 6.

### Terminology

Different developer communities use different terms for the same concept, which can be confusing. Here is a mapping.

| CMF             | XML/XSD                                                | JSON (JSON-LD) | RDF+OWL         |
| :-------------- | :----------------------------------------------------- | -------------- | :-------------- |
| Class           | Complex type with complex content (CCC)                | Type           | Class           |
| Datatype        | Complex type with simple content (CSC), or Simple type | Type           | Datatype        |
| Object property | Element declaration with a CCC type                    | Key            | Object property |
| Data property   | Attribute declaration                                  | Key            | Data property   |
| Object          | Element with complex content                           | Object         | Resource        |
| Value           | Element with simple content, or attribute              | Literal        | Literal         |

### The ugly JSON problem

Elements in NIEM XML with complex content have a very nice NIEM JSON equivalent.  So do elements with simple content but no attributes.  The resulting JSON can be processed as plain JSON or as JSON-LD, and seems natural to developers.

```
<ns:Object>                                       | "ns:Object": {    
  <ns:DataProp>Hello</ns:DataProp>                |   "ns:DataProp": [
  <ns:DataProp>I</ns:DataProp>                    |     "Hello",      
  <ns:DataProp>Must</ns:DataProp>                 |     "I",          
  <ns:DataProp>Be</ns:DataProp>                   |     "Must",       
  <ns:DataProp>Going</ns:DataProp>                |     "Be",         
</ns:Object>                                      |     "Going",      
                                                  |   ]               
                                                  | }                 
```

Elements with simple content and attributes must be represented in JSON as an object.  That object must have two keys, one for the attribute, one for the literal value.  The message model in NIEM 5.0 XSD does not have a name for the literal, so we have to use a made-up key like `rdf:value`.  That made-up key is the first ugliness.  JSON-LD developers might tolerate it, but plain JSON developers won't like it at all.

```
<ns:Object>                                       | "ns:Object": {           
  <ns:DataProp ns:att="x">Hello</ns:DataProp>     |   "ns:DataProp": [       
</ns:Object>                                      |    {                     
                                                  |      "ns:att": "x",      
                                                  |      "rdf:value": "Hello"
                                                  |    }                     
                                                  |   ]                      
                                                  | }                                             
```

In NIEM 5.0, attributes are *possible* on every element, including elements with simple content.  But attributes are usually not *actually present* on elements with simple content in the runtime data.  We are thus faced witht two choices for NIEM JSON, both ugly.  

1. Elements with simple content that *might* have attributes are *always* represented as JSON objects.  This leads to ugly JSON like this:

   ```
   <ns:Object>                                   | "ns:Object": {               
     <ns:DataProp>Hello</ns:DataProp>            |   "ns:DataProp": [           
     <ns:DataProp>I</ns:DataProp>                |     { "rdf:value": "Hello" },
     <ns:DataProp>Must</ns:DataProp>             |     { "rdf:value": "I" },    
     <ns:DataProp>Be</ns:DataProp>               |     { "rdf:value": "Must" }, 
     <ns:DataProp>Going</ns:DataProp>            |     { "rdf:value": "Be" },   
   </ns:Object>                                  |     { "rdf:value": "Going" },
                                                 |   ]                          
                                                 | }                            
   ```

2. Only the elements that *actually have* attributes are represented as objects; the others are literals.  But then JSON developers have to write code to examine each value to see if it is an object or a literal.  That is also ugly.

   ```
   <ns:Object>                                    | "ns:Object": {           
     <ns:DataProp ns:att="x">Hello</ns:DataProp>  |   "ns:DataProp": [       
     <ns:DataProp>I</ns:DataProp>                 |    {                     
     <ns:DataProp>Must</ns:DataProp>              |      "ns:att": "x",      
     <ns:DataProp>Be</ns:DataProp>                |      "rdf:value": "Hello"
     <ns:DataProp>Going</ns:DataProp>             |    },                    
   </ns:Object>                                   |     "I",                 
                                                  |     "Must",              
                                                  |     "Be",                
                                                  |     "Going",             
                                                  |   ]                      
                                                  | }           
   ```

In NIEM 6 we are solving the ugly JSON problem in two ways.  First, we are removing the *structural* attributes from all of the CSC types.  The attributes that remain in NIEM XSD are the *semantic* attributes.  There are hundreds of CSC types in the NIEM model; something like 35 of these  have semantic attributes, and these attributes are often removed in a message model subset.  This means that most elements with simple content will always convert to JSON literals.  Some elements with simple content will always convert to JSON objects – and the message model tells developers which those are, so they can code appropriately for them.  Second, the CMF model now defines a property for the literal value formerly represented as `rdf:value`, like this:

```
<ns:Object>                                      | "ns:Object": {           
  <ns:DataProp ns:att="x">Hello</ns:DataProp>    |   "ns:DataProp": [       
</ns:Object>                                     |    {                     
                                                 |      "ns:att": "x",      
                                                 |      "ns:DataPropLiteral": "Hello"
                                                 |    }                     
                                                 |   ]                      
                                                 | }                        
```

As a result, NIEM 6 allows us to clearly distinguish between object properties and data properties in the CMF message model, and to represent each in NIEM XML, NIEM JSON, and NIEM RDF, in a way that is natural to the respective developer communities.

### IC-ISM and NIEM 3.0

NIEM 3.0 made provision for the Information Security Marking (ISM) and Need-To-Know (NTK) standards from the US Intelligence Community (IC).  The IC wants to put certain attributes on every element, like this:

```
<nc:PersonName ism:classification="S" ism:ownerProducer="US">
```

We provided for ISM and NTK by hacking the structures namespace, as follows:

```
  <xs:complexType name="ObjectType" abstract="true">
    .
    .
    .
    <xs:anyAttribute namespace="urn:us:gov:ic:ism urn:us:gov:ic:ntk" processContents="lax"/>
  </xs:complexType>
```

The `xs:anyAttribute` hack answered the mail and kept us out of the ISM business (which is a snakepit).  NIEM said, in effect, whatever you need to do with ISM and NTK attributes is not our business but is OK with us.  It's a hack, of course, because what were we going to do when the *next* guys came along with the magic attributes *they* wanted to add to every object?

If we retain that hack in NIEM 6, then we have potential attributes in every CSC type definition, and we're right back into the ugly JSON swamp.  So we need to find a different solution.

### Attributes on augmentation elements

NIEM 5 supports attribute augmentations through the existing augmentation element mechanism.   If I wanted to augment `nc:PersonName` with `ism:classification`, I'd put this into my extension namespace:

```
<xs:element name="PersonNameAugmentation" substitutionGroup="nc:PersonNameAugmentationPoint" 
            type="my:PersonNameAugmentionType>
<xs:complexType name="PersonNameAugmentationType">
  <xs:complexContent>
    <xs:extension base="s:AugmentationType">
      <xs:attribute ref="ism:classification" use="required"/>
```

and the runtime XML would look like this

```
<nc:PersonName>
  <nc:PersonFullName>Superman
  <my:PersonNameAugmentation ism:classification="S"/>
```

First, that won't work for ISM.  ISM attributes must be on the main element, not on some funky child element.  Second, this is so clunky.  Why should anyone put up with this, if we can arrange something better.  And i believe we can… 

### Attribute augmentations in NIEM 6

Augmentations are a way for the owners of one namespace to add properties to a class in another namespace.  We already have everything we need in CMF to augment an existing NIEM class with ISM attributes.  For instance, if you wanted to add the `ism:classification` attribute to every element of`nc:PersonType` in your message model, you would write this CMF:

```
<Class s:id="nc.PersonNameType">
  <Name>Object>
  <Namespace s:ref="nc"/>
  <HasProperty>
    <Property s:ref="nc.PersonFullName">
      <MinOccursQuantity>1</MinOccursQuantity>
      <MaxOccursQuantity>1</MaxOccursQuantity>
    </Property>
    <Property s:ref="ism.classification">
      <MinOccursQuantity>1</MinOccursQuantity>
      <MaxOccursQuantity>1</MaxOccursQuantity>
      <AugmentationPropertyNamespace s:ref="mymsg"/>
    </Property>
  </HasProperty>
</Class>
<Property s:id="ism.classification">
  <Name>classification</Name>
  <Namespace s:ref="ism"/>
  <Datatype s:ref="xs.string"/>
  <AttributeIndicator>true</AttributeIndicator>
</Property>
```

From that CMF, we would generate something like this XSD within the *niem-core.xsd* schema document (not implemented yet):

```
<xs:complexType name="PersonNameType">
  <xs:complexContent>
    <xs:extension base="s:ObjectType">
      <xs:sequence>
        <xs:element ref="nc:PersonFullName"/>
      <xs:sequence>
      <xs:attribute ref="ism:classification" use="required" appinfo:augmentedBy="http://example.com/mymsg/"/>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
```

And of course, this approach is *not*  a hack; it works for anyone who needs attribute augmentations, not just the IC.  Everybody happy?  Not quite.  A schema document for a namespace in the NIEM model is supposed to be a subset of the reference schema document.  Any message that is valid against the schema constructed from the message model schema document should also be valid against a schema constructed from the reference schema document.  That rule is broken here.  The reference schema document for NIEM Core does not permit `ism:classification` attributes in elements of type `nc:PersonType`.

If we wanted to augment `nc:PersonType` with an *element*, there would be no difficulty.  We have an elaborate augmentation mechanism in XSD which honors the subset rule for augmentation *elements*.  Alas, XSD does not support a similar mechanism for augmentation *attributes*.  It can't be done in the same way – there's no substitution for attributes.

### Wildcard for attribute augmentations?

One way to support attribute augmentations while also preserving the schema subset principle is through an attribute wildcard.  That's a lot like the ISM hack in NIEM 3.0, but with important differences:

```
  <xs:complexType name="ObjectType" abstract="true">
    <xs:sequence>
      <xs:element ref="structures:ObjectAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="structures:id"/>
    <xs:attribute ref="structures:ref"/>
    <xs:attribute ref="structures:uri"/>
    <xs:attribute ref="structures:metadata"/>
    <xs:attribute ref="structures:relationshipMetadata"/>
    <xs:attribute ref="structures:sequenceID"/>
    <xs:anyAttribute namespace="##other" processContents="strict"/>
  </xs:complexType>
```

This allows the message model designer to augment any class with any attribute property, not just ISM and NTK properties.  But the runtime message won't be valid unless those augmenting attributes are defined somewhere in the message schema.

### Proposal

I see four ways forward:

1. We decline to support ISM and NTK in NIEM 6.0 and beyond
2. We put up with ugly JSON for everyone
3. We give up the strict subset rule in order to support attribute augmentations OR
   We use an attribute wildcard with strict validation
4. Somebody comes up with something I can't think of

I think maybe #3 is good enough that we don't need #4.

### Do we still need `structures:ObjectType`?

Every object class in NIEM XSD is a complex type definition that extends some type in the structures namespace, usually `structures:ObjectType`.  This is mainly a way to include pointer and reference attributes on objects.  That does not change the model semantics.  It's XML specific.  So at present, CMFTool does not include the structures namespace when converting from NIEM XSD.

Working through the ISM problem reminded me that the object classes in the structures namespace all have augmentation points.  In NIEM XSD it's possible to augment every class in the model, like this:

```
<xs:complexType name="EverythingAugmentationType">
  <xs:complexContent>
    <xs:extension base="s:AugmentationType">
      <xs:sequence>
        <xs:element ref="my:CoolProperty"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
<xs:element name="CoolProperty" substitionGroup="s:ObjectAugmentationPoint"/>
```

I don't believe anyone has ever done that in a message model.  But it's possible.  And it's kinda the obvious way to handle ISM attributes via attribute augmentation.  So it seems that perhaps CMF models do require a *ur-class* from which every model class is derived.  Is there a better alternative?

If we do need a *ur-class*, what should it be named?  I was really happy to remove "structures" from CMF models, because most everything in that namespace is (1) XML-specific, and (2) unrelated to semantics.  So I dislike the idea of making every class derived from `structures:ObjectType`.  Since it's model-related, wouldn't it make more sense to have a `nc:ObjectType` in the core?



Author:  Scott Renner
Date: 18 January 2023
