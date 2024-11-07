# Metadata mechanism options

Starting assumptions:

* We need metadata attributes for relationship metadata
* We need metadata attributes for metadata on simple content
* We sometimes want to express metadata properties as ordinary child elements 

What are our choices?  I want to begin by examining the different meanings of a child element with complex content in NIEM XML.  At present the NDR defines three possible meanings:  a related object, an unrelated object, and a collection of related properties.

**A related object**

When the child object is derived from `s:ObjectType` or `s:AssociationType`, then it asserts both the existence of an object and a relationship between that object and the parent.  The type of the child element  defines the object's class.  The name of the child element describes the relationship from the parent to the child. This is the ordinary case.

```
<my:Person>                         |      _:n0 a my:PersonType .
  <my:OwnsItem>                     |      _:n0 my:OwnsItem _:n1 .
</my:Person>                        |      _:n1 a my:ItemType .
```

**An unrelated object**

When the child object is derived from `s:MetadataType`, it only asserts the existence of an object.  There is no relationship from the parent to the child.  

```
<my:Person>                         |      _:n0 a my:PersonType .                        
  <my:Metadata>                     |      _:n1 a my:MetadataType .
</my:Person>                        |
```

A relationship to an object derived from `s:MetadataType` is asserted only by a `s:metadata` or `s:relationshipMetadata` pointer.

``` 
<my:Person s:metadata="md">         |     _:n0 a my:PersonType .
  <my:Metadata s:id="md">           |     _:n0 s:metadata _:md .
</my:Person>                        |     _:md a my:MetadataType .
```

**A collection of properties**

When the child object is derived from `s:AugmentationType`, it does not assert the existence of an object at all.  Instead it asserts a relationship from the parent to each member of a collection of properties, which may be objects or literals, and among which no relationship is asserted.

```
<my:Person>                         |     _:n0 a my:PersonType .
  <my:PersonAugmentation>           |     _:n0 j:PersonAdultIndicator "true" .
    <j:PersonAdultIndicator>true    |     _:n0 j:PersonClothing _:n1 .
    <j:PersonClothing>              |     _:n1 a j:ClothingType .
      <j:ClothingColorText>Red      |     _:n1 j:ClothingColorText "red" .
    </j:PersonClothing>             |
  </my:PersonAugmentation>          |
</my:Person>                        |
```

By the way, it's fine to have two augmentation types that add the same property to an element.  But the fact that the property appears in different augmentation elements is not meaningful.

```
<my:Person>                         |     _:n0 a my:PersonType .
  <my:PersonAugmentation>           |     _:n0 j:PersonDexterityText "agile" .
    <j:PersonDexterityText>agile    |     _:n0 j:PersonDexterityText "fast" .
  </my:PersonAugmentation>          |
  <your:PersonAugmentation>         |
    <j:PersonDexterityText>fast     |
  </your:PersonAugmentation>        |
</my:Person>                        |
```

**OK, so what?**

Thus sayeth NDR 5.0.  That's our starting point.  Now I want to describe two things we would like to have, and then explore our options for having them.  

* Sometimes we want our metadata element to be an unrelated child (usually of the message element) because we are referring to it elsewhere with a metadata attribute.  Sometimes we want our metadata element to be a related child of the element it modifies.  We would like to use the same metadata element in both ways.  That is not possible in NIEM 5.  How can we do both in NIEM 6?

* Sometimes our metadata element is a collection of properties, like an augmentation.  Sometimes it is an object with properties of its own.  In NIEM 5 we have no way to tell one from the other.  How can we distinguish these in NIEM 6?

  

**Metadata element as either related or unrelated child**

In NIEM 5, metadata is always optional so far as schema validation is concerned.  If we want to use XSD to say that an element *must* have certain metadata properties, then we need a way to make a metadata element the related child of its parent.  In NIEM 6 we could do that with one metadata element or with two metadata elements.

If we want to use a single metadata element in both ways -- as a related and as an unrelated child -- then there must be some attribute in the instance message that lets us tell the difference.  I see three  possibilities:

1.  We could use the  `s:id` attribute.  A metadata element with `s:id` is an unreladed child; a metadata element without is a related child.  Seems a bit like a hack, using one attribute in two ways to mean different things.  The data might look like this:

``` 
<ex:CrashDriverInfo>                        |   _:n0 a ex:CrashDriverInfoType .
  <ex:Metadata>                             |   _:n0 ex:Metadata _:n1 .
    <nc:ReportedDate>2022-04-05             |   _:n0 j:CrashPersonInjury _:n2 .
  </ex:Metadata>                            |   _:n1 a ex:MetadataType .
  <ex:Metadata s:id="m01">                  |   _:n1 nc:ReportedDate "2022-04-05" .
    <nc:ReportedDate>2022-05-06             |   _:n2 a j:CrashPersonInjuryType .
  </ex:Metadata>                            |   _:n2 ex:Metadata _:m01 .
  <j:CrashPersonInjury s:metadata="m01">    |   _:m01 a ex:MetadataType .
    . . .                                   |   _:m01 nc:ReportedDate "2022-05-06" .
```

2. We could create a new `s:metadataID` attribute, defined only for `s:MetadataType`, which would no longer have `s:id`, `s:uri` or `s:ref`.  Only elements with this attribute may be the target of a metadata attribute.  A metadata element without the attribute is a related child.  Somewhat less hackiferous.
3. The last option is to leave `s:id` alone and create a new `s:thisIsARelatedChild` attribute, or something similar.  I include this option for completeness.

If you don't like any of those choices, then we need two different elements of the same metadata type, distinguished by some appinfo into the schema.  The schema might look like:

```
<xs:element name="MetadataRef" type="ex:MetadataType"/>
<xs:element name="MetadataChild" type="ex:MetadataType>
  <xs:annotation>
    <xs:appinfo>
      <appinfo:RelatedChildIndicator>true
```

And then the data would look like:

```
<ex:CrashDriverInfo>                        |   _:n0 a ex:CrashDriverInfoType .
  <ex:MetadataChild>                        |   _:n0 ex:MetadataChild _:n1 .
    <nc:ReportedDate>2022-04-05             |   _:n0 j:CrashPersonInjury _:n2 .
  </ex:Metadata>                            |   _:n1 a ex:MetadataType .
  <ex:MetadataRef s:id="m01">               |   _:n1 nc:ReportedDate "2022-04-05" .
    <nc:ReportedDate>2022-05-06             |   _:n2 a j:CrashPersonInjuryType .
  </ex:Metadata>                            |   _:n2 ex:MetadataRef _:m01 .
  <j:CrashPersonInjury s:metadata="m01">    |   _:m01 a ex:MetadataType .
    . . .                                   |   _:m01 nc:ReportedDate "2022-05-06" .
```

I think that exhausts our choices for NIEM 6.



**Distinguishing related objects and property collections**

Every element derived from `s:AugmentationType` is a property collection.  Some elements derived from `s:MetadataType` are property collections, and some are independent objects.  For example, `nc:Metadata` is usually a property collection.

```
<ex:CrashDriverInfo s:metadata="m1">        |   _:n0 a ex:CrashDriverInfoType .
  <nc:Metadata s:id="m1">                   |   _:n0 nc:Comment "Boy what a big one" .
    <nc:Comment>Boy what a big one!         |   _:n0 nc:ExpirationDate "2022-04-05" .
    <nc:ExpirationDate>2022-04-05           |   
  </nc:Metadata>                            |  
```

In this example there is no relation between the comment and the expiration date, and no purpose for an independent metadata object.  On the other hand, sometimes `nc:Metadata` has to be an independent object.

```
<ex:CrashDriverInfo s:metadata="m1 m2">     |   _:n0 a ex:CrashDriverInfoType .
  <nc:Metadata s:id="m1">                   |   _:n0 nc:Metadata _:m1 .
    <nc:ReportedDate>2022-01-02             |   _:n0 nc:Metadata _:m2 .
    <nc:ReportingPersonText>Fred            |   _:m1 a nc:MetadataType .
  </nc:Metadata>                            |   _:m1 nc:ReportedDate "2022-01-02" .
  <nc:Metadata s:id="m2">                   |   _:m1 nc:ReportingPersonText "Fred" .
    <nc:ReportedDate>2022-03-01             |   _:m2 a nc:MetadataType .
    <nc:ReportingPersonText>Bob             |   _:m2 nc:ReportedDate "2022-03-01" .
  </nc:Metadata>                            |   _:m2 nc:ReportingPersonText "Bob"
```

In this example the parent object was reported twice, by different people at different times.  The time and person are related, and so an independent metadata object is necessary.

I would like to distinguish between property collections and independent objects in the schema, perhaps through appinfo applied to the type definition, like this:

```
<xs:element name="ReportingMetadata" type="nc:ReportingMetadataType"/>
<xs:complexType name="ReportingMetadataType">
  <xs:annotation>
    <xs:appinfo>
      <appinfo:PropertyCollectionIndicator>false
  <xs:complexContent>
    <xs:extension base="s:MetadataType">
      <xs:sequence>
        <xs:element ref="nc:ReportedDate" minOccurs="0"/>
        <xs:element ref="nc:ReportingOrganizionAbstract" minOccurs="0"/>
        <xs:element ref="nc:ReportingPersonRoleAbstract" minOccurs="0"/>   
        <xs:element ref="nc:ReportingPersonAbstract" minOccurs="0"/>
        
<xs:element name="MetadataCollection" type="nc:MetadataCollectionType"/>
<xs:complexType name="MetadataCollectionType">
  <xs:annotation>
    <xs:appinfo>
      <appinfo:PropertyCollectionIndicator>true
  <xs:complexContent>
    <xs:extension base="s:MetadataType">
      <xs:sequence>
        <xs:element ref="nc:AdministrativeID" minOccurs="0"/>
        . . .
```

But this would require reworking the NIEM model.  It's not a small change.



Author:  Dr. Scott Renner | MITRE | sar@mitre.org | DRAFT | 23 May 2022
