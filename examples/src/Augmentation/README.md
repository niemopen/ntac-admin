**Attribute augmentation and metadata replacement**

This paper collects everything I've written about wildcards for attribute augmentations, corrects everything I now know to be wrong, and walks through the simplest examples I can construct.  At the end you should understand how (I think!) augmentation should work in NIEM 6, why that is a complete replacement for the metadata mechanism, and what all this means for XSD conformance targets.

**Bottom line up front**

Here is a summary of the important changes from NIEM 5.  The examples will show why these changes will work and are necessary.

* Source schemas and message schemas have different *structures.xsd* and `niem-xs.xsd` 
  * For source schemas, those schema documents have attribute wildcards
  * For message schemas, they do not
* Message schemas do not need proxy types
* *TBD*

**Examples and what they illustrate**

The examples are message specifications that illustrate augmentations to `nc:EducationType`.  There are four kinds of augmentation in NIEM 6, depending on whether you are augmenting complex content or simple content, and whether your augmentation is an element or attribute.

I put the source code (XSD and CMF) for the examples into the ntac-admin repo.  Bullets in italics are not finished yet.  You will find:

* 00-N5-Subset – a NIEM 5 subset schema created with SSGT
* 01-N6-Subset – the equivalent subset with NIEM 6 namespaces
* 02-NoAug – message schema for the base case, no augmentations in this message spec
* 03-AugCCwithE – message schema for complex content augmented with an element
* *04-AugCCwithA – message schema for complex content augmented with an attribute*
* *05-AugSCwith A – message schema for simple content augmented with an attribute*
* *06-AugSCwithE – message schema for simple content augmented with an element*

**00-N5-Subset:  A NIEM 5.2 subset schema and model** 

Generated with SSGT using the wantlist provided.  I ran all the schema documents through "cmftool xcanon" to make them easier to read.  Contains *n5sub.cmf*, which is a NIEM 6 CMF file describing the NIEM 5.2 subset.  Yes, that works, CMFTool understands NIEM 5 (and 4, 3, and 2) just fine.

Note that in CMF, augmentations just appear in the model like any other property; for example, look at the Class object for `nc:EducationType`.  The augmentation properties are marked.  They are also listed in the augmenting Namespace object.

**01-N6-Subset:  A NIEM 6 subset schema  and model**

I ran *n5sub.cmf* through `cmftool n5to6`to convert the model to NIEM 6 namespaces.  It's not perfect but it's a lot faster than editing all the namespace URIs by hand.  Then I edited the resulting *n6sub.cmf* to

* Fix the JXDM version number; cmftool isn't smart enough to do that
* Change the conformance targets to `SubsetSchemaDocument` (there are no documentation elements, etc.)
* Put "subset" into the `SchemaVersionText` properties.

I ran *n6sub.cmf* through `cmftool m2xsrc` to generate the NIEM 6 XSD *source schema*.  A source schema is composed of reference, extension, and subset schema documents.  The attribute wildcards in *structures.xsd* and *niem-xs.xsd* are what make this a source schema.  

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

There is nothing new in NIEM 6 about this kind of augmentation.  I tried to replace augmentation points with wildcards and was shot down by the Unambiguous Particle Attribution rule; discussion [here](https://github.com/niemopen/ntac-admin/discussions/32#discussioncomment-5091246).  We do need a new NDR to say that augmentation elements may not be repeated – that is, while the schema allows the following, the NDR will not.

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
* I also added a CMFTool option to designate a "message schema namespace" and then make sure it imports everything needed that isn't imported somewhere else.  Fun piece of code!

***04-AugCCwithA – message schema for complex content augmented with an attribute***

***05-AugSCwith A – message schema for simple content augmented with an attribute***

***06-AugSCwithE – message schema for simple content augmented with an element***
