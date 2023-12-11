**Attribute augmentation and metadata replacement**

This paper collects everything I've written about wildcards for attribute augmentations, corrects everything I now know to be wrong, and walks through the simplest examples I can construct.  At the end you should understand how (I think!) augmentation should work in NIEM 6, why that is a complete replacement for the metadata mechanism, and what all this means for XSD conformance targets.

**Bottom line up front**

Here is a summary of the important changes from NIEM 5.  The examples will show why these changes will work and are good and/or necessary.

* Subset schemas and message schemas are different kinds of NIEM model, represented in XSD
  * A subset schema is a collection of model components intended for extension and reuse
  * A message schema defines the content of a message format; not intended for extension
  * There is no difference in the corresponding CMF

* A subset schema has attribute wildcards in *structures.xsd* and *niem-xs.xsd*
  * `<xs:anyAttribute processContents="strict"/>` says that any type may be augmented with an attribute; we promise to define that attribute in the message schema
  * The IC-ISM hack is removed

* A message schema has no wildcards in *structures.xsd*
  * Types in *structures.xsd* include individual attributes as needed.  There is no `SimpleObjectAttributeGroup`
  * Message schemas do not need proxy types and so do not need *niem-xs.xsd*

* Attribute augmentations are defined in the model
  * By `appinfo:augmentingNamespace` in XSD
  * By an `AugmentationRecord` in CMF

* There is a new *reference attribute* concept and a new `Ref` representation term.  An attribute named `fooRef` has a list of references to elements of type `FooType` (or a derived type).
* `appinfo:relationshipPropertyIndicator` describes a property that modifies the relationship between its parent and grandparent object.  It does the same thing as `structures:relationshipMetadata`, but works for any property, not just metadata elements.
* `structures:appliesToParent` is false for an element that is not a property of its parent.
* There is no longer any need for the metadata mechanism.  Everything that can be done with `@metadata` and `@relationshipMetadata` can also be done with augmentations.

**Examples and what they illustrate**

The examples are message specifications that illustrate augmentations to `nc:EducationType`.  There are four kinds of augmentation in NIEM 6, depending on whether you are augmenting complex content or simple content, and whether your augmentation is an element or attribute.

I put the source code (XSD and CMF) for the examples into the ntac-admin repo.  You will find:

* 00-N5-Subset – a NIEM 5 subset schema created with SSGT
* 01-N6-Subset – the equivalent subset with NIEM 6 namespaces
* 02-NoAug – message schema for the base case, no augmentations in this message spec
* 03-AugCCwithE – message schema for complex content augmented with an element
* 04-AugCCwithA – message schema for complex content augmented with an attribute
* 05-AugSCwith A – message schema for simple content augmented with an attribute
* 06-AugSCwithE – message schema for simple content augmented with an element
* 07-AugOTwithE – augmenting every object with an element *(BROKEN at this time)*
* 08-AugOTwithA – augmenting every object with an attribute *(BROKEN at this time)*

**00-N5-Subset:  A NIEM 5.2 subset schema and model** 

Generated with SSGT using the wantlist provided.  I ran all the schema documents through "cmftool xcanon" to make them easier to read.  Contains *n5sub.cmf*, which is a NIEM 6 CMF file describing the NIEM 5.2 subset.  Yes, that works, CMFTool understands NIEM 5 (and 4, 3, and 2) just fine.

Note that in CMF, augmentations just appear in the model like any other property; for example, look at the Class object for `nc:EducationType`.  The augmentation properties are marked.  They are also listed in the augmenting Namespace object.

**01-N6-Subset:  A NIEM 6 subset schema  and model**

I ran *n5sub.cmf* through `cmftool n5to6`to convert the model to NIEM 6 namespaces.  It's not perfect but it's a lot faster than editing all the namespace URIs by hand.  Then I edited the resulting *subset.cmf* to

* Fix the Justice schema document name and version number; cmftool isn't smart enough to do that
* Change the conformance targets to `SubsetSchemaDocument` (there are no documentation elements, etc.)
* Put "subset" into the `SchemaVersionText` properties.

I ran *subset.cmf* through `cmftool m2xs` to generate the NIEM 6 XSD *subset schema*.  A source schema is composed of reference, extension, and subset schema documents.  The attribute wildcards in *structures.xsd* and *niem-xs.xsd* are what make this a subset schema.  

**02-NoAug:  A message schema without augmentations **

Contains a NIEM 6 message schema.  Messages in this format look like this:

```
<my:Message 
 xmlns:my="http://example.com/N6AugEx/1.0/"
 xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/">
  <nc:PersonEducation>
    <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
</my:Message>
```

As simple as it can be.  There are no augmentations in this message specification, so the augmentation elements do not appear in the CMF or XSD.  We don't even need the JXDM schema document.

The *absence* of attribute wildcards in *structures.xsd* and *niem-xs.xsd* is part of what makes this a message schema.  A message schema has a different `structures.xsd` with no attribute wildcards.  In this message specification the reference attributes (`id`, `ref`, `uri`, and `qname`) are part of `structures:ObjectType`.  A message schema doesn't need `niem-xs.xsd` and doesn't have proxy types at all.

**03-AugCCwithE:  A message schema with element augmentation on complex content**

This message specification ugments the complex content type `nc:EducationType` with the`j:EducationTotalYearsText` element.  Messages look like this:

```
<my:Message 
  xmlns:j="https://docs.oasis-open.org/niemopen/ns/model/domains/jxdm/8.2/"
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/">
  <nc:PersonEducation>
    <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
    <j:EducationAugmentation>
      <j:EducationTotalYearsText>5</j:EducationTotalYearsText>
    </j:EducationAugmentation>
  </nc:PersonEducation>
</my:Message>
```

There is nothing new in NIEM 6 about this kind of augmentation.  I tried to replace augmentation points with wildcards; it doesn't work. (Discussion of the Unambiguous Particle Attribution rule is [here](https://github.com/niemopen/ntac-admin/discussions/32#discussioncomment-5091246).) 

```
  <nc:PersonEducation>
    <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
    <j:EducationAugmentation>
      <j:EducationTotalYearsText>5</j:EducationTotalYearsText>
    </j:EducationAugmentation>
    <j:EducationAugmentation>
      <j:EducationTotalYearsText>19</j:EducationTotalYearsText>
    </j:EducationAugmentation>    
  </nc:PersonEducation>
```

Schema assembly is a PITA and can be especially tricky with augmentations.  CMFTool does not generate unneeded import elements in NIEM XSD, so what you get here by default is

* *messageModel.xsd*, which imports *niem-core.xsd*
* *niem-core.xsd*, which imports nothing
* *jxdm.xsd*, which isn't imported by anything

And when you do what everybody does, use *message-model.xsd* to validate *msg1.xml*, it fails.  Ain't nobody ever heard of `j:EducationAugmentation`.  What to do, what to do?

* Xerces does the right thing if you give it an XML Catalog file.  So I added a CMFTool option to generate one.

* I also added a CMFTool option to designate a "message schema namespace" and then make sure it imports everything needed that isn't imported somewhere else.  Fun piece of code!  The command is

  `ct m2xm -c --msgNS http://example.com/N6AugEx/1.0/ -o xsd augCCwE.cmf`

**04-AugCCwithA – message schema for complex content augmented with an attribute**

This message specification augments the complex content type `nc:EducationType` with the attribute `my:privacyText`.  Messages look like this:

```
<my:Message 
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/">
  <nc:PersonEducation my:privacyText="PUBLIC">
    <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
</my:Message>
```

You can't do this in NIEM 5.  You can do it in NIEM 6 because the base types in *structures.xsd* have `xs:anyAttribute processContents="strict"/>`, which says "there may be other attributes and they will be defined in the schema".  The augmenting attribute looks like this in *messageModel.xsd*:

```
<xs:attribute name="privacyText" type="xs:token"/>
```

Nothing special there.  The augmentation actually occurs in *niem-core.xsd*:

```
<xs:complexType name="EducationType">
  <xs:complexContent>
    <xs:extension base="structures:ObjectType">
      <xs:sequence>
        <xs:element ref="nc:EducationDescriptionText" minOccurs="0" maxOccurs="unbounded"/>
        <xs:element ref="nc:EducationInProgressIndicator" minOccurs="0" maxOccurs="unbounded"/>
        <xs:element ref="nc:EducationAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
      </xs:sequence>
      <xs:attribute ref="my:privacyText" appinfo:augmentingNamespace="http://example.com/N6AugEx/1.0/"/>
    </xs:extension>
 </xs:complexContent>
</xs:complexType>
```

Observe the new appinfo that is documenting the augmentation.  In CMF, that documentation happens in two places.  First, with the AugmentationNamespace property in the Class object:

```
  <Class structures:id="nc.EducationType">
    <Name>EducationType</Name>
    <Namespace structures:ref="nc" xsi:nil="true"/> ...
    <HasProperty>
      <Property structures:ref="my.privacyText" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>1</MaxOccursQuantity>
      <AugmentationNamespace structures:ref="my"/>
    </HasProperty>
  </Class>
```

Second, with the AugmentRecord object in the Namespace object for the augmenting namespace:

```
<Namespace structures:id="my">
  <NamespaceURI>http://example.com/N6AugEx/1.0/</NamespaceURI>
  <NamespacePrefixText>my</NamespacePrefixText>
  <NamespaceKindCode>EXTENSION</NamespaceKindCode>
  <AugmentRecord>
    <Class structures:ref="nc.EducationType" xsi:nil="true"/>
    <Property structures:ref="my:privacyText" xsi:nil="true"/>
    <AugmentationIndex>-1</AugmentationIndex>
    <MinOccursQuantity>0</MinOccursQuantity>
    <MaxOccursQuantity>1</MaxOccursQuantity>
  </AugmentRecord>    
</Namespace>
```

There isn't anything in *messageModel.xsd* to document the augmentation created by the owner of this namespace.  We could add some documenting appinfo, looking like this:

```
<xs:schema
  targetNamespace="http://example.com/N6AugEx/1.0/" ...>
  <xs:annotation>
    <xs:appinfo>
      <appinfo:AugmentationRecord>
        <appinfo:AugmentedType>nc.EducationType
        <appinfo:AugmentingProperty>my:privacyText
```

Is it worth it?  Adds documentation and also complexity.  Think about it.

**05-AugSCwith A – message schema for simple content augmented with an attribute**

This message specification augments the simple content type `nc:TextType` with the attribute `my:privacyText`.  Messages look like this:

```
<my:Message 
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/">
  <nc:PersonEducation>
    <nc:EducationDescriptionText my:privacyText="PUBLIC">PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
</my:Message>
```

You can't do this in NIEM 5.  You can do it in NIEM 6 because the base types in *structures.xsd* have `xs:anyAttribute processContents="strict"/>`, which says "there may be other attributes and they will be defined in the schema".  The augmentation actually happens in *niem-core.xsd*:

```
<xs:complexType name="TextType">
  <xs:simpleContent>
    <xs:extension base="xs:string">
      <xs:attribute ref="my:privacyText" appinfo:augmentingNamespace="http://example.com/N6AugEx/1.0/"/>
    </xs:extension>
  </xs:simpleContent>
</xs:complexType>
```

Because `nc:TextType` now has an attribute, it is a Class in CMF.

```
<Class structures:id="nc.TextType">
  <Name>TextType</Name>
  <Namespace structures:ref="nc" xsi:nil="true"/>
  <HasProperty>
    <Property structures:ref="nc.TextLiteral" xsi:nil="true"/>
    <MinOccursQuantity>1</MinOccursQuantity>
    <MaxOccursQuantity>1</MaxOccursQuantity>
  </HasProperty>
  <HasProperty>
    <Property structures:ref="my.privacyText" xsi:nil="true"/>
    <MinOccursQuantity>0</MinOccursQuantity>
    <MaxOccursQuantity>1</MaxOccursQuantity>
    <AugmentationNamespace structures:ref="my" xsi:nil="true"/>
  </HasProperty>
</Class>
```

Documentation of the augmentation is the same as in the previous example.

**06-AugSCwithE – message schema for simple content augmented with an element**

This message augments the simple content `nc:TextType` with the element `my:PrivacyAssertion`.  Messages look like this:

```
<my:Message 
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/"
  xmlns:structures="https://docs.oasis-open.org/niemopen/ns/model/structures/6.0/">
  <nc:PersonEducation>
    <nc:EducationDescriptionText my:privacyAssertionRef="p01">PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
  <my:PrivacyAssertion structures:id="p01" structures:onlyRef="true">
    <nc:Date>2023-07-01</nc:Date>
    <my:PrivacyText>PUBLIC</my:PrivacyText>
  </my:PrivacyAssertion>
</my:Message>
```

You can't do this in NIEM 5.  We can make it work in NIEM 6 wth new rules, new appinfo, a new structures attribute, and new RDF interpretations.  The new rules apply to *reference attributes*, and they will go something like this:

* an attribute ending in `Ref` must be a reference attribute, and vice versa
* a refrence attribute must have type="xs:IDREFS"
* for each IDREF value of a reference attribute named `fooRef`, there must be an element with the same ID that is derived from `FooType`.

The new appinfo attribute is `@relationshipPropertyIndicator`.  Remember all the fun around relationship metadata?  This appinfo does the same thing, but isn't limited to metadata; it can be used on any property.  You don't *have* to apply it to a reference attribute, but we need it here, because the privacy assertion isn't about the education description "PhD", it's about the relationship betwen the `PersonEducation` object and that description.  In *messageModel.xsd* we have

```
<xs:attribute name="privacyAssertionRef" type="xs:IDREFS" appinfo:relationshipPropertyIndicator="true"/>
```

The new structures attribute is `@onlyRef`.  It asserts that an element is not a property of its parent.  We use it here because the privacy assertion does not apply to the entire message, only to the `nc:EducationDescriptionText` element,

The RDF* interpretation of the message looks like this:

```
01  _:n0 a my:MessageType .
02  _:n0 nc:PersonEducation _:n1 .
03  _:n1 a nc:EducationType .
04  _:n1 nc:EducationDescriptionText _:n2 {| my:PrivacyAssertion _:n3 |} .
05  _;n1 nc:EducationInProgressIndicator "false" .
06  _:n2 a nc:EducationDescriptionText .
07  _:n2 nc:TextLiteral "PhD" .
08  _:n3 a my:PrivacyAssertionType .
09  _:n3 nc:Date "2023-07-01" .
10  _:n3 my:PrivacyText "PUBLIC" .
```

Without `appinfo:relationshipPropertyIndicator`, instead of line 04, we would have

```
_:n1 nc:EducationDescriptionText _:n2 .
_:n2 nc:TextLiteral "PhD" .
_:n2 my:PrivacyAssertion _:n3 .
```

Without `structures:onlyRef`, we would have

```
_:n0 my:PrivacyAssertion _:n3 .
```

**Replacing metadata with augmentations**

Metadata on simple content looks a lot like the above example. 

```
<my:Message 
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/"
  xmlns:structures="https://docs.oasis-open.org/niemopen/ns/model/structures/6.0/">
  <nc:PersonEducation>
    <nc:EducationDescriptionText structures:relationshipMetadata="p01">PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
  <my:PrivacyMetadata structures:id="p01" structures:onlyRef="true">
    <nc:Date>2023-07-01</nc:Date>
    <my:PrivacyText>PUBLIC</my:PrivacyText>
  </my:PrivacyMetadata>
</my:Message>
```

Everything that can be done with metadata on simple content can be done just as well with an reference attribute augmentation.  Metadata on complex content can be replaced with augmentations in two ways.  Instead of using metadata, like this:

```
<my:Message 
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/"
  xmlns:structures="https://docs.oasis-open.org/niemopen/ns/model/structures/6.0/">
  <nc:PersonEducation  structures:relationshipMetadata="p01">
    <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
  <my:PrivacyMetadata structures:id="p01" structures:onlyRef="true">
    <nc:Date>2023-07-01</nc:Date>
    <my:PrivacyText>PUBLIC</my:PrivacyText>
  </my:PrivacyMetadata>
</my:Message>
```

You can use an object augmentation, like this:

```
<my:Message 
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/"
  xmlns:structures="https://docs.oasis-open.org/niemopen/ns/model/structures/6.0/">
  <nc:PersonEducation  structures:relationshipMetadata="p01">
    <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
    <my:EducationAugmentation>
      <my:PrivacyAssertion>
        <nc:Date>2023-07-01</nc:Date>
        <my:PrivacyText>PUBLIC</my:PrivacyText>
      </my:PrivacyAssertion>
    </my:EducationAugmentation>
  </nc:PersonEducation>
</my:Message>
```

Or you could use a reference attribute, like this:

```
<my:Message 
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/"
  xmlns:structures="https://docs.oasis-open.org/niemopen/ns/model/structures/6.0/">
  <nc:PersonEducation my:privacyAssertionRef="p01">
    <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
  <my:PrivacyAssertion structures:id="p01" structures:onlyRef="true">
    <nc:Date>2023-07-01</nc:Date>
    <my:PrivacyText>PUBLIC</my:PrivacyText>
  </my:PrivacyAssertion>
</my:Message>
```

You get the same RDF either way.  So everything that can be done with metadata on complex content can also be done just as well with augmentations.  There's an opportunity here to drop a lot of complexity from the NDR, and I say we should grab it.

**07-AugOTwithE – Augmenting every type in the model with an element**

You do this in NIEM 5 and NIEM 6 by writing an augmentation for `structures:ObjectType`.  Messages look like this:

```
<my:Message 
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/"
  xmlns:structures="https://docs.oasis-open.org/niemopen/ns/model/structures/6.0/">
  <nc:PersonEducation>
    <my:ObjectAugmentation>
      <my:PrivacyAssertion>
        <nc:Date>2023-08-05</nc:Date>
        <my:PrivacyText>PUBLIC</my:PrivacyText>
      </my:PrivacyAssertion>
    </my:ObjectAugmentation>
    <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
</my:Message>
```

The XSD for this is the same in NIEM 5 and NIEM 6.  The CMF looks like this:

```
<Model ...>
  <AugmentRecord>
    <Property structures:ref="my.PrivacyAssertion" xsi:nil="true"/>
    <AugmentationIndex>0</AugmentationIndex>
    <MinOccursQuantity>0</MinOccursQuantity>
    <MaxOccursQuantity>1</MaxOccursQuantity>
  </AugmentRecord>
  <Namespace structures:id="my">
    <NamespaceURI>http://example.com/N6AugEx/1.0/</NamespaceURI>
    <NamespacePrefixText>my</NamespacePrefixText>
    <NamespaceKindCode>EXTENSION</NamespaceKindCode>
  </Namespace> ...
```

The trick is to treat an element augmentation of every type as a property of the entire model.  With this approach I don't need to have everything derived from a new `nc:ObjectType`, and I don't have to write the structures namespace into the CMF file, so I pass GO and collect $200.

This works even if we decide to keep `structures:AssociationType`,   Just gets a bit more complicated.  The CMF would then look like:

```
<Model ...>
  <ObjectAugmentation>
    <AugmentRecord>
      <Property structures:ref="my.PrivacyAssertion" xsi:nil="true"/>
      <AugmentationIndex>0</AugmentationIndex>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>1</MaxOccursQuantity>
    </AugmentRecord>
  </ObjectAugmentation>
  <AssociationAugmentation> ...
  <Namespace structures:id="my"> ...
```

**08-AugOTwithA – Augmenting every type in the model with an attribute**

Let's suppose I want to put my `privacyText` attribute on every element.  The message would look like this:

```
<my:Message my:privacyText="PUBLIC"
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/">
  <nc:PersonEducation my:privacyText="PUBLIC">
    <nc:EducationDescriptionText my:privacyText="PUBLIC">PhD</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator my:privacyText="PUBLIC">false</nc:EducationInProgressIndicator>
  </nc:PersonEducation>
</my:Message>
```

You can't do this in NIEM 5.  It takes two steps in NIEM 6.  To put `privacyText` on complex content, we write the attribute augmentation into *structures.xsd*, like this:

```
<xs:complexType name="ObjectType" abstract="true">
  <xs:sequence>
    <xs:element ref="structures:ObjectAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
  </xs:sequence>
  <xs:attribute ref="structures:id"/>
  <xs:attribute ref="structures:qname"/>
  <xs:attribute ref="structures:ref"/>
  <xs:attribute ref="structures:uri"/>
  <xs:attribute ref="my:privacyText" appinfo:augmentingNamespace="http://example.com/N6AugEx/1.0/"/>
</xs:complexType>
```

To put `privacyText` on simple content, we put it in `SimpleObjectAttributeGroup`in structures.xsd, and then include that attribute group on every simple content type. Usually the easiest way to do that is to use proxy types.

And the CMF looks like this:

```
<Model ...>
  <AugmentRecord>
    <Property structures:ref="my.privacyText" xsi:nil="true"/>
    <AugmentationIndex>-1</AugmentationIndex>
    <MinOccursQuantity>0</MinOccursQuantity>
    <MaxOccursQuantity>1</MaxOccursQuantity>
  </AugmentRecord>
  <Namespace structures:id="my">
    <NamespaceURI>http://example.com/N6AugEx/1.0/</NamespaceURI>
    <NamespacePrefixText>my</NamespacePrefixText>
    <NamespaceKindCode>EXTENSION</NamespaceKindCode>
  </Namespace> ...
```

This approach allows us to support IC-ISM without the old horrid attribute wildcard hack.  I'll work up an example someday.  

**Conclusion**

By putting attribute wildcards into the subset schema, but not into the message schema, we are able to support

* augmenting complex content with an element
* augmenting complex content with an attribute
* augmenting simple content with an element
* augmenting simple content with an attribute

Wildcards in the subset schema allow attribute augmentation while still preserving the subset schema principle.  (Everything valid againt the subset schema document is also valid against the reference or extension schema document.)

No wildcards in the message schema means it precisely defines the mandatory and optional content of the message format.

Augmentation in NIEM 6 an do everything the metadata mechanism does in NIEM 5.  One way to do things is better than two ways.  Fewer complex things to explain is better than more.  We should remove the metadata mechanism from NIEM 6.



Author:  Scott Renner
Revised:  2023-08-11
