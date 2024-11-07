# Implementing augmentations in NIEM 6

BLUF: We don't need attribute wildcards in *structures.xsd* after all.

#### Element augmentations are cool. Alas, attribute augmentations are not possible in NIEM 5. The closest thing you have is that ugly ISM wildcard hack in *structures.xsd:*

```
<xs:complexType name="ObjectType" abstract="true">
  . . .
  <xs:anyAttribute namespace="urn:us:gov:ic:ism urn:us:gov:ic:ntk" processContents="lax"/>
</xs:complexType>
```

#### That hack only works for ISM. What if I want to augment somebody else's type with `my:privacyText`? You aren't going to hack everyone's pet rock into *structures.xsd*, are you?

NIEM 6 has support for attribute augmentations! If you want to add `my:privacyText` to a type in somebody else's namespace, you do it like this:

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

#### So the augmentation happens in the schema document for the other guy's namespace. Isn't there something in my namespace that shows the augmentation? There is when I do an element augmentation.

In CMF, attribute augmentations are recorded in the namespace object for the augmentation namespace, like this:

```
<Namespace structures:id="my">
  <NamespaceURI>http://example.com/N6AugEx/1.0/</NamespaceURI>
  . . .
  <AugmentRecord>
    <Class structures:ref="nc.EducationType" xsi:nil="true"/>
    <DataProperty structures:ref="my.privacyText" xsi:nil="true"/>
    <AugmentationIndex>-1</AugmentationIndex>
    <MinOccursQuantity>0</MinOccursQuantity>
    <MaxOccursQuantity>1</MaxOccursQuantity>
  </AugmentRecord>
</Namespace>
```

At present, attribute augmentations are not recorded in XSD. We could record attribute augmentations in the schema-level appinfo for the augmenting namespace, like this:

```
<xs:schema
  targetNamespace="http://example.com/N6AugEx/1.0/"
  xmlns:my="http://example.com/N6AugEx/1.0/"
  xmlns:cmf="https://docs.oasis-open.org/niemopen/ns/specification/cmf/1.0/"
  . . .
  xml:lang="en-US">
  <xs:annotation>
    <xs:appinfo>
      <cmf:AugmentRecord cmf:class="nc:EducationType">
        <xs:attribute ref="my:privacyText">
      </cmf:AugmentRecord>      
    </xs:appinfo>
    . . .
</xs:schema>
```

But so far no one has said that juice is worth the squeeze. And I have plenty of other things to implement in CMFTool.

#### So you really just stuff your attibute into the schema document for somebody else's namespace? Cool! Say, why couldn't I do that in NIEM 5?

That was forbidden by **Rule 4-1: Fundamental NIEM Subset Rule** in the [IEPD Specification](https://niem.github.io/MPD-Spec/v5.0/niem-iepd-spec.html)

> **[Rule 4-1] (Schema-subset) (Constraint)**\
A schema document subset ($SUBSET) for a given reference schema document set ($REFERENCE) MUST be defined such that for all instance XML documents ($XML), where $XML is valid to $SUBSET, $XML is valid to $REFERENCE.

Basically, when you're editing the schema document in your IEPD for a namespace that belongs to somebody else, you can take things out, but you can't put things in. 

Note that the rule assumes the only thing you would subset is the NIEM model, which is why the rule says "reference schema document set".  But a message designer might include components from extension schemas in other message specifications, so in NIEM 6 the term would be *reuse schema document set*. 

#### Wait, NDR 5 doesn't forbid stuffing attributes into a schema document for somebody else's namespace?

Nope. I looked hard. I don't think there's anything applicable in NDR 5. The applicable rule is in the IEPD specification. And it's kind of squishy. A good rules lawyer could argue his way around it.

#### So how did you satisfy the subset rule with attribute augmentation in NIEM 6?

We preserved the **Fundamental Subset Rule** by inserting attribute wildcards into the *structures.xsd* schema document in a *reuse schema document set*, like this:

```
<xs:complexType name="ObjectType" abstract="true">
  <xs:annotation>
    <xs:documentation>A data type for a thing with its own lifespan that has some existence.</xs:documentation>
  </xs:annotation>
  <xs:sequence>
    <xs:element ref="structures:ObjectAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
  </xs:sequence>
  <xs:attribute ref="structures:appliesToParent"/>
  <xs:attribute ref="structures:id"/>
  <xs:attribute ref="structures:ref"/>
  <xs:attribute ref="structures:uri"/>
  <xs:anyAttribute processContents="strict" namespace="##other"/>
</xs:complexType>
```

We put the same attribute wildcard into `AssociationType` and `SimpleObjectAttributeGroup`. As a result, when you have an instance document with content like this:

```
<nc:PersonEducation my:privacyText="PUBLIC">
  <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
  <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
</nc:PersonEducation>
```

it will be valid against a reuse schema document set including the canonical *niem-core.xsd* and *structures.xsd* with the wildcards, so we pass GO and collect $200.

#### Doesn't that mean there are no limits on the attributes that can appear in a message?

No, because we are careful to remove those attribute wildcards from *structures.xsd* in a *message schema document set*.

#### I see. So you need NDR rules for a *reuse schema document set*, and for a *message schema document set*.  And you have two versions of *structures.xsd* floating around.  Wouldn't it be easier to just rewrite the Fundamental Subset Rule so that it allows attribute augmentations?

Umm... yes. Yes, I do believe it would. I believe the rules in section 9.4 of the [latest NDR6 draft](https://github.com/niemopen/niem-naming-design-rules/blob/dev/ndr-v6.0-psd01.html) already say everything that needs to be said about the subset rule. And if we aren't trying to satisfy the *Fundamental Subset Rule* in the NIEM 5 IEPD specification, then we don't need the attribute wildcards in *structures.xsd*.

#### Quite a bit of complexity then goes away.

Yes. If this idea survives the NTAC sanity check, we will remove the wildcards from *structures.xsd* in PS02.

#### Hey, couldn't you do the same thing with element augmentations? 

You mean, instead of a *justice.xsd* containing this augmentation element and type:

```
<xs:complexType name="EducationAugmentationType">
  <xs:annotation>
    <xs:documentation>A data type for additional information about an education.</xs:documentation>
  </xs:annotation>
  <xs:complexContent>
    <xs:extension base="structures:AugmentationType">
      <xs:sequence>
        <xs:element ref="j:EducationTotalYearsText" minOccurs="0" maxOccurs="unbounded"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
<xs:element name="EducationAugmentation" type="j:EducationAugmentationType" 
    substitutionGroup="nc:EducationAugmentationPoint">
  <xs:annotation>
    <xs:documentation>Additional information about an education.</xs:documentation>
  </xs:annotation>
</xs:element>
```

and a message like this:

```
<nc:PersonEducation>
  <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
  <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  <j:EducationAugmentation>
    <j:EducationTotalYearsText>5</j:EducationTotalYearsText>
  </j:EducationAugmentation>
</nc:PersonEducation>
```

We could instead make the element augmentation in the same way we are making attribute augmentations, like this:

```
  <xs:complexType name="EducationType">
    <xs:annotation>
      <xs:documentation>A data type for a person's educational background.</xs:documentation>
    </xs:annotation>
    <xs:complexContent>
      <xs:extension base="structures:ObjectType">
        <xs:sequence>
          <xs:element ref="nc:EducationDescriptionText" minOccurs="0" maxOccurs="unbounded"/>
          <xs:element ref="nc:EducationInProgressIndicator" minOccurs="0" maxOccurs="unbounded"/>
          <xs:element ref="j:EducationTotalYearsText" minOccurs="0" maxOccurs="unbounded" 
            appinfo:augmentingNamespace="https://docs.oasis-open.org/niemopen/ns/model/domains/justice/6.0/"/>
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
```

and a message like this:

```
<nc:PersonEducation>
  <nc:EducationDescriptionText>PhD</nc:EducationDescriptionText>
  <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
  <j:EducationTotalYearsText>5</j:EducationTotalYearsText>
</nc:PersonEducation>
```

Is that the idea?

#### Yes. It removes an astounding amount of complexity from NIEM.

Well, it would do so, if it works out. No one has yet thought it through.

It is certainly a very large change to the NIEM 6 model. There are a *lot* of augmentation points, elements, and types in the reference schema documents. We would have to replace them all, in three steps:

1. Augmentation elements and types become schema-level appinfo in the schema document for the augmenting namespace, as shown above.

2. Augmentation points are removed. The augmenting elements are *not* added to the schema document for the augmented namespace. In this way, each reference schema document remains an independent configuration item for a namespace in the NIEM model; that is, *niem-core.xsd* stays a reference schema document defining content for NIEM Core and nothing else; *justice.xsd* defines content for NIEM Justice and nothing else; and so forth. (That would not be the case if element augmentations were stuffed into the reference schema document for the augmented namespace.)

3. A subset generation tool smart enough to recognise `cmf:AugmentationRecord` elements in CMF or XSD appinfo, offer augmentation properties for selection, and generate subset schema documents that include the selected augmentation properties. 

CMFTool could handle #1 and #2. It could handle #3, except for the UI change.  For #3, the subset selection UI interface would look something like this:

```
[ ] nc:EducationType
      [ ] nc:EducationDescriptionText
      [ ] nc:EducationInProgressIndicator
      [ ] j:EducationActivity                   # Justice domain augmentation
      [ ] j:EducationCourse                     # Justice domain augmentation
      [ ] j:EducationTotalYearsText             # Justice domain augmentation
```

instead of

```
[ ] nc:EducationType
      [ ] nc:EducationDescriptionText
      [ ] nc:EducationInProgressIndicator
      [ ] nc:EducationAugmentationPoint
```

and also, somewhere else

```
[ ] j:EducationAugmentation
[ ] j:EducationAugmentationType
      [ ] j:EducationActivity
      [ ] j:EducationCourse
      [ ] j:EducationTotalYearsText
```

Attractive, isn't it? We are doing a lot of work to support XSD as a modeling formalism. The **Fundmendal Subset Rule** is responsible for much of that work.

I have a dream, and in that dream NIEM 7 does not support XSD as a modeling formalism, and then everything is SO MUCH SIMPLER.

\
Author: Scott Renner\
Last modified: 2024-10-04

<style>
    pre { 
      background-color:#f0f0f0;
      padding: 6px;
      font-size: smaller; }
    code { background-color: #f0f0f0; }
</style>