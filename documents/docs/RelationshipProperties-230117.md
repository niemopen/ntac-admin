# Relationship properties for IC-ISM in NIEM 6

I am not here attempting to fix IC-ISM, or to tell the IC how their classification markings should work in structured data.  But I do observe that their `ism:classification` attribute could be a lot like NIEM relationship metadata.  Here is an illustration (with some tags omitted for clarity):

```
<ns:NewspaperEmployees>
  <nc:Person>
    <nc:PersonName>
      <nc:PersonFullName>Clark Kent
    </nc:PersonName>
    <nc:PersonName ism:classification="S">
      <nc:PersonFullName>Superman
    </nc:PersonName>
  </nc:Person>
</ns:NewspaperEmployees>
<ns:SuperHeroes>
  <nc:Person>
    <nc:PersonName>
      <nc:PersonFullName>Superman
    </nc:PersonName>
  </nc:Person>
</ns:SuperHeroes>
```

It seems plain that here the `ism:classification` attribute is meant to apply to the relationship between the Person and the `Superman` PersonName.  It's not that the very name "Superman" is classified.  That name actually appears in the (unclassified) list of superheroes.  So the RDF for this XML should look like

```
_:n1 nc:PersonName _:n2 .
_:n1 nc:PersonName _:n3 {| ism:classification "S" |} .
_:n2 nc:PersonFullName "Clark Kent" .
_:n3 nc:PersonFullName "Superman" .
```

and not like

```
_:n1 nc:PersonName _:n2 .
_:n1 nc:PersonName _:n3 .
_:n2 nc:PersonFullName "Clark Kent" .
_:n3 nc:PersonFullName "Superman" .
_:n3 ism:classification "S" .
```

and the NIEM JSON should look like

```
{
  "nc:PersonName" : [
    {
      "nc:PersonFullName": "Clark Kent"
    },
    {
      "nc:PersonFullName": "Superman",
      "@annotation": {
        "ism:classification": "S"
      }
    }
  ]
}
```

instead of

```
{
  "nc:PersonName" : [
    {
      "nc:PersonFullName": "Clark Kent"
    },
    {
      "nc:PersonFullName": "Superman",
      "ism:classification": "S"
    }
  ]
}
```

Again, not trying to fix ISM for the IC.  But this does look like a useful capability, for other properties even if not for ISM.  So I think we need a way to say that a property applies to a relationship instead of an object.  In CMF that might look like

```
<Class>
  <Name>PersonType</Name>
  <Namespace s:ref="nc"/>
  <HasProperty>
    <Property s:ref="nc.PersonName"/>
    <MinOccursQuantity>1
    <MaxOccursQuantity>unbounded
  </HasProperty>
  <HasProperty>
    <Property s:ref="ism.classification"/>
    <MinOccursQuantity>1
    <MaxOccursQuantity>1
    <RelationshipPropertyIndicator>true
  </HasProperty>  
</Class>
<Property>
  <Name>classification
  <Namespace s:ref="ism"/>
  <AttributeIndicator>true
</Property>
```

and in XSD could be appinfo like this

```
<xs:complexType name="PersonName">
  <xs:complexContent>
    <xs:extension base="s:ObjectType">
      <xs:sequence>
        <xs:element ref="nc:PersonFullName"/>
      </xs:sequence>
      <xs:attribute ref="ism:classification" use="required" appinfo:RelationshipPropertyIndicator="true"/>
```

`RelationshipPropertyIndicator`is documentation telling developers how to interpret the property.  It would drive the generation of JSON Schema for validating NIEM JSON, and would control conversion of message data at runtime (between XML, JSON, RDF, etc.).  It seems likely that somebody will want this capability, if we can make it work.



Author:  Scott Renner
Date:  17 January 2023