# Using `xsi:type` instead of `substitutionGroup`

## NIEM and XSD

NIEM uses XML Schema (XSD) as its modeling formalism.  Semantics of NIEM XSD are defined by the Naming and Design Rules, which map XSD constructs to the RDF triples they entail.

NIEM uses substitution groups to represent data model subproperties.  Substitution groups are also the mechanism permitting the authors of the model namespace `X` to provide a new representation for a concept defined in a different namespace `Y` – without changing the schema document for `Y`.

For instance, the NIEM Core namespace defines a concept for a person, and for a person's hair color, like this:

```
<xs:complexType name="PersonType">
  <xs:annotation>
    <xs:documentation>A data type for a human being.</xs:documentation>
  </xs:annotation>
  <xs:complexContent>
    <xs:extension base="structures:ObjectType">
      <xs:sequence>
        <xs:element ref="nc:PersonHairColorAbstract" minOccurs="0" maxOccurs="unbounded"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
<xs:element name="PersonHairColorAbstract" abstract="true">
  <xs:annotation>
    <xs:documentation>A data concept for a color of the hair of a person.</xs:documentation>
  </xs:annotation>
</xs:element>
<xs:element name="PersonHairColorText" type="nc:TextType" substitutionGroup="nc:PersonHairColorAbstract">
  <xs:annotation>
    <xs:documentation>A color of the hair of a person.</xs:documentation>
  </xs:annotation>
</xs:element>
```

That model supports messages like this:

```
<nc:Person>
  <nc:PersonHairColorText>brownish</nc:PersonHairColorText>
  <nc:PersonHairColorText xml:lang="fr">marron</nc:PersonHairColorText>
</nc:Person>
```

Free text for hair color does not satisfy the users in the Justice domain.  They need an enumeration.  The authors of the Justice namespace in the NIEM model created that enumeration, without changing the NIEM Core schema document, like this.  (Some enumerations from the actual code list are omitted for brevity):

```
<xs:simpleType name="PersonHairColorCodeSimpleType">
  <xs:annotation>
    <xs:documentation>A data type for a code set identifying a hair color of a person.</xs:documentation>
  </xs:annotation>
  <xs:restriction base="xs:token">
    <xs:enumeration value="BALD">
      <xs:annotation>
        <xs:documentation>BALD</xs:documentation>
      </xs:annotation>
    </xs:enumeration>
    <xs:enumeration value="BLACK">
      <xs:annotation>
        <xs:documentation>BLACK</xs:documentation>
      </xs:annotation>
    </xs:enumeration>
    <xs:enumeration value="BROWN">
      <xs:annotation>
        <xs:documentation>BROWN</xs:documentation>
      </xs:annotation>
    </xs:enumeration>
  </xs:restriction>
</xs:simpleType>
<xs:complexType name="PersonHairColorCodeType">
  <xs:annotation>
    <xs:documentation>A data type for a code set identifying a hair color of a person.</xs:documentation>
  </xs:annotation>
  <xs:simpleContent>
    <xs:extension base="j:PersonHairColorCodeSimpleType">
      <xs:attributeGroup ref="structures:SimpleObjectAttributeGroup"/>
    </xs:extension>
  </xs:simpleContent>
</xs:complexType>
<xs:element name="PersonHairColorCode" type="j:PersonHairColorCodeType" 
    substitutionGroup="nc:PersonHairColorAbstract">
  <xs:annotation>
    <xs:documentation>A code set identifying a hair color of a person.</xs:documentation>
  </xs:annotation>
</xs:element
```

Now the NIEM model supports messages like this:

```
<nc:Person>
  <nc:PersonHairColorText xml:lang="fr">marron</nc:PersonHairColorText>
  <j:PersonHairColorCode>BROWN</j:PersonHairColorCode>
</nc:Person>
```

## NIEM and CMF

Here is the same model in the new NIEM Common Model Format (CMF).  Observe the three properties, `j:PersonHairColorCode` and `nc:PersonHairColorText`, both subproperties of `nc:PersonHairColorAbstract`.

```
<Property structures:id="j.PersonHairColorCode">
  <Name>PersonHairColorCode</Name>
  <Namespace structures:ref="j" xsi:nil="true"/>
  <DefinitionText>A code set identifying a hair color of a person.</DefinitionText>
  <SubPropertyOf structures:ref="nc.PersonHairColorAbstract" xsi:nil="true"/>
  <Datatype structures:ref="j.PersonHairColorCodeType" xsi:nil="true"/>
</Property>
<Property structures:id="nc.PersonHairColorAbstract">
  <Name>PersonHairColorAbstract</Name>
  <Namespace structures:ref="nc" xsi:nil="true"/>
  <DefinitionText>A data concept for a color of the hair of a person.</DefinitionText>
  <AbstractIndicator>true</AbstractIndicator>
</Property>
<Property structures:id="nc.PersonHairColorText">
  <Name>PersonHairColorText</Name>
  <Namespace structures:ref="nc" xsi:nil="true"/>
  <DefinitionText>A color of the hair of a person.</DefinitionText>
  <SubPropertyOf structures:ref="nc.PersonHairColorAbstract" xsi:nil="true"/>
  <Datatype structures:ref="nc.TextType" xsi:nil="true"/>
</Property>
<Class structures:id="nc.PersonType">
  <Name>PersonType</Name>
  <Namespace structures:ref="nc" xsi:nil="true"/>
  <DefinitionText>A data type for a human being.</DefinitionText>
  <HasProperty>
    <Property structures:ref="nc.PersonHairColorAbstract" xsi:nil="true"/>
    <MinOccursQuantity>0</MinOccursQuantity>
    <MaxOccursQuantity>unbounded</MaxOccursQuantity>
  </HasProperty>
</Class>
<Datatype structures:id="j.PersonHairColorCodeType">
  <Name>PersonHairColorCodeType</Name>
  <Namespace structures:ref="j" xsi:nil="true"/>
  <DefinitionText>A data type for a code set identifying a hair color of a person.</DefinitionText>
  <RestrictionOf>
    <Datatype structures:ref="xs.token" xsi:nil="true"/>
    <Enumeration>
      <StringValue>BALD</StringValue>
      <DefinitionText>BALD</DefinitionText>
    </Enumeration>
    <Enumeration>
      <StringValue>BLACK</StringValue>
      <DefinitionText>BLACK</DefinitionText>
    </Enumeration>
    <Enumeration>
      <StringValue>BROWN</StringValue>
      <DefinitionText>BROWN</DefinitionText>
    </Enumeration>
  </RestrictionOf>
</Datatype>      
```

## NIEM and RDF

The same model can be expressed in RDF with vocabulary from RDFS and OWL, as follows:

```
j:PersonHairColorCode
    a owl:DataProperty ;
    rdfs:range j:PersonHairColorCodeType ;
    rdfs:subPropertyOf nc:PersonHairColorAbstract ;
    rdfs:comment "A code set identifying a hair color of a person." .

nc:PersonHairColorAbstract
    a owl:DataProperty ;
    rdfs:comment "A data concept for a color of the hair of a person." .

nc:PersonHairColorText
    a owl:DataProperty ;
    rdfs:range nc:TextType ;
    rdfs:subPropertyOf nc:PersonHairColorAbstract ;
    rdfs:comment "A color of the hair of a person." .

j:PersonHairColorCodeType
    a rdfs:Datatype ;
    rdfs:comment "A data type for a code set identifying a hair color of a person." ;
    owl:equivalentClass [
        a rdfs:Datatype ;
        owl:onDatatype xsd:token  ;
        owl:oneOf (
            "BALD"
            "BLACK"
            "BROWN"
        )
    ] .
```

The XML messages also have an RDF equivalent.

```
<nc:Person>                                      | _:n1 a nc:PersonType ;                     
  <nc:PersonHairColorText>Kinda brown</nc:Person |      nc:PersonHairColorText "marron"@fr;
  <j:PersonHairColorCode>BROWN</j:PersonHairColo |      j:PersonHairColorCode "BROWN" .
</nc:Person>                                     |                                       
```

From the model and message RDF, a reasoner would be able to infer

```
_:n1 nc:PersonHairColorAbstract "brown"@fr, "BROWN" .
```

## Why not use`xsi:type` instead?

Instead of

```
<nc:Person>
  <nc:PersonHairColorText>Kinda brown</nc:PersonHairColorText>
  <j:PersonHairColorCode>BROWN</j:PersonHairColorCode>
</nc:Person>
```

we could have

```
<nc:Person>
  <nc:PersonHairColorAbstract xsi:type="nc:TextType">Kinda brown</nc:PersonHairColorAbstract>
  <nc:PersonHairColorAbstract xsi:type="j:PersonHairColorCodeType>BROWN</nc:PersonHairColorAbstract>
</nc:Person>
```

OK, that won't validate.  We'll have to remove `@abstract="true"` and change the name to `nc:PersonHairColor`.  

```
<nc:Person>
  <nc:PersonHairColor xsi:type="nc:TextType">Kinda brown</nc:PersonHairColor>
  <nc:PersonHairColor xsi:type="j:PersonHairColorCodeType>BROWN</nc:PersonHairColor>
</nc:Person>
```

Now we get RDF like this:

```
_:n1 a nc:PersonType ;
     nc:PersonHairColor "Kinda brown" , "BROWN" .
```

We lost the distinct properties with their distinct types.  More importantly, the possible types for `PersonHairColor` are no longer specified in the schema.  There is nothing to stop the message author from writing something like this:

```
<nc:Person>
  <nc:PersonHairColor xsi:type="j:AircraftStyleCode">DP</nc:PersonHairColor>
</nc:Person>
```

That has valid syntax but garbage semantics.  This is the reason why NIEM 6 will use substitution groups in the XSD model representations.  What we can offer is tool support to generate an implementation schema that is more suitable for XML binding (e.g. JAXB) and validation, in which all substitution groups are replaced with `xs:choice`, like this:

```
<xs:complexType name="PersonType">
  <xs:annotation>
    <xs:documentation>A data type for a human being.</xs:documentation>
  </xs:annotation>
  <xs:complexContent>
    <xs:extension base="structures:ObjectType">
      <xs:sequence>
        <!--<xs:element ref="nc:PersonHairColorAbstract" minOccurs="0" maxOccurs="unbounded"/>-->
        <xs:choice minOccurs="0" maxOccurs="unbounded">
          <xs:element ref="j:PersonHairColorCode"/>
          <xs:element ref="nc:PersonHairColorText"/>
        </xs:choice>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
```



Author: Scott Renner
Date:  16 November 2022
