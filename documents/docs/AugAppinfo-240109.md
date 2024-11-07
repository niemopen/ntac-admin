## New appinfo for attribute augmentation

An augmentation in a reference schema document expresses an *opinion.*  For example, in the opinion of the Justice domain, `j:EducationTotalYearsText` is a possibly useful property for `nc:EducationType`.  An augmentation doesn't have real effect until a message designer selects it for his subset and puts it into a message schema.

Suppose you are writing a reference schema document and want to say that element `Bar` is a possibly useful property in some `FooType`.  Then you declare an element substitutable for FooAugmentationPoint. Simple.

Suppose you are writing a reference schema document and want to say that attribute `bar` is a possibly useful property in some `FooType`.  Now what do you do?  Umm… well, nothing, at present.  There is no way to do it.  Attribute augmentations take effect in the *augmented* schema document; for example, line 9 in this *niem-core.xsd* fragment:

```
01 <xs:complexType name="EducationType">
02   <xs:complexContent>
03     <xs:extension base="structures:ObjectType">
04       <xs:sequence>
05         <xs:element ref="nc:EducationDescriptionText" minOccurs="0" maxOccurs="unbounded"/>
06         <xs:element ref="nc:EducationInProgressIndicator" minOccurs="0" maxOccurs="unbounded"/>
07         <xs:element ref="nc:EducationAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
08       </xs:sequence>
09       <xs:attribute ref="my:privacyText" appinfo:augmentingNamespace="http://example.com/N6AugEx/1.0/"/>
10     </xs:extension>
11   </xs:complexContent>
12 </xs:complexType>
```

We need a new bit of appinfo to fill this hole.  It goes into the *augmenting* schema document, and looks like this:

```
<xs:annotation>
  <xs:appinfo>
    <appinfo:AttributeAugmentation augmented="nc:EducationType" attribute="my:privacyText"/>
  </xs:appinfo>
</xs:annotation>
```

The declaration in *appinfo.xsd* looks like this:

```
<xs:element name="AttributeAugmentation">
  <xs:annotation>
    <xs:documentation>A declaration of a component augmented with an attribute.</xs:documentation>
  </xs:annotation>
  <xs:complexType>
    <xs:attribute name="augmented" type="xs:QName"/>
    <xs:attribute name="attribute" type="xs:QName"/>
  </xs:complexType>
</xs:element>
```

I have modified the augmentation examples in the ntac-admin repo accordingly.  Look at:

* Augmenting complex content with an attribute ([04-AugCCWithA](https://github.com/niemopen/ntac-admin/tree/main/examples/src/Augmentation/04-AugCCWithA))
* Augmenting simple content with an attribute ([05-AugSCWithA](https://github.com/niemopen/ntac-admin/tree/main/examples/src/Augmentation/05-AugSCWithA))