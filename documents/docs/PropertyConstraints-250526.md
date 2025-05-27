### Cardinality constraints for properties

NIEM 6 replaced `RoleOf` properties with URL references. So, for example, in NIEM 5 you might have

```
<nc:Person structures:id="P01"
  <nc:PersonName nc:personNameCommentText="copied">
    <nc:PersonGivenName>Peter</nc:PersonGivenName>
    <nc:PersonMiddleName>Death</nc:PersonMiddleName>
    <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
    <nc:PersonSurName>Wimsey</nc:PersonSurName>
  </nc:PersonName>
</nc:Person>
<j:CrashPerson>
  <nc:RoleOfPerson structures:ref="P01" xsi:nil="true"/>
  <j:CrashPersonInjury>
    <nc:InjuryDescriptionText>Broken Arm</nc:InjuryDescriptionText>
    <j:InjurySeverityCode>3</j:InjurySeverityCode>
  </j:CrashPersonInjury>
</j:CrashPerson>
```

but in NIEM 6 that would be

```
<nc:Person structures:uri="#P01"
  <nc:PersonName nc:personNameCommentText="copied">
    <nc:PersonGivenName>Peter</nc:PersonGivenName>
    <nc:PersonMiddleName>Death</nc:PersonMiddleName>
    <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
    <nc:PersonSurName>Wimsey</nc:PersonSurName>
  </nc:PersonName>
</nc:Person>
<j:CrashPerson structures:uri="#P01">
  <j:CrashPersonInjury>
    <nc:InjuryDescriptionText>Broken Arm</nc:InjuryDescriptionText>
    <j:InjurySeverityCode>3</j:InjurySeverityCode>
  </j:CrashPersonInjury>
</j:CrashPerson>
```

What happens when you want to ensure that `nc:Person` has at least one `nc:PersonName` property?  Well, what you *can't* do is bang `minOccurs="1"` into `nc:PersonType`.  Why?  Because `j:CrashPersonType` inherits from `nc:PersonType`, that's why.

So I think pretty soon we will want to support cardinality constraints on individual properties.  This won't be terribly hard to do in CMF.  You would write something like this:

```
  <ObjectProperty structures:id="nc.Person">
    <Name>Person</Name>
    <Namespace structures:ref="nc" xsi:nil="true"/>
    <DocumentationText>A human being.</DocumentationText>
    <Class structures:ref="nc.PersonType" xsi:nil="true"/>
    <ChildPropertyConstraint>
      <Property structures:id="nc.PersonName" xsi:nil="true"/>
      <MinOccursQuantity>1</MinOccursQuantity>
    </ChildPropertyConstraint>
  </ObjectProperty>
```

But how to represent that in XSD?  You pretty much have to do it with appinfo.  Perhaps something like this:

```
<xs:element name="Person" type="nc:PersonType">
 <xs:annotation>
  <xs:appinfo>
   <xs:element ref="nc:PersonName" minOccurs="1"/>
  </xs:appinfo>
 </xs:annotation>
</xs:element>
```

When we transform that model into a message schema, we would then use a local type, like this:

```
<xs:element name="Person">
 <xs:complexType>
  <xs:sequence>
   <xs:element ref="nc:PersonName" minOccurs="1"/>
```

And that's perhaps the best we can do in NIEM 6.  What we *really* want are cardinality constraints for objects.  We want to say that every object of `nc:PersonType` has at least one `nc:PersonName` property.  We don't really care where that property appears in the XML or JSON data. We could *say* that with SHACL, like this:

```
_:Shape01
    a sh:NodeShape ;
    sh:targetClass nc:PersonType ;  # Apply the shape to all instances of nc:PersonType
    sh:property [
        sh:path nc:PersonName ;     # Specify the property to check
        sh:minCount 1 ;             # Require at least one occurrence of nc:PersonName
    ] .
```

We could even work that into the CMF and XSD for `nc:PersonType`. But I don't think we can validate that constraint using XML Schema or JSON Schema, alas.