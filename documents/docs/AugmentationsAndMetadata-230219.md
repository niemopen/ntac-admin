# Augmentations and Metadata in NIEM 6

This is a discussion paper for the OASIS NIEMOpen Technical Architecture committee.

Here is a revised proposal for wildcard augmentation and how it can replace the metadata mechanism.  It resolves to my satisfaction (well, so far) the problem of applying metadata to simple content.  Once I reframed this as "how to augment simple content with an object property", the solution appeared.

I believe a message designer should be able to augment complex and simple content with element and attribute properties.  You can't do all of those things in NIEM 5.  In this proposal for NIEM 6, those things are done as follows:

| I want to augment | With an attribute               | With an element                       |
| ----------------- | ------------------------------- | ------------------------------------- |
| Complex content   | Wildcard attribute augmentation | Wildcard element augmentation         |
| Simple content    | Wildcard attribute augmentation | Wildcard attribute reference *(NEW!)* |

**Dependencies:** This proposal depends primarily on making a big distinction between reference models and message models, and between reference schemas and message schemas.  I have found this distinction very useful when talking with developers who have trouble with NIEM schemas.  And we are going to want it anyway for the message specification guidance.

1. A *reference model* provides names and definitions for concepts, and relations among them.  They are characterized by "optionality and over-inclusiveness".  That is, they define more concepts than needed for any particular data exchange specification, without cardinalty constraints or unnecessary datatype facet constraints, so it is easy to select the concepts that are needed and omit the rest.  They fill some, perhaps all, of the roles of an ontology.  They do not specify the content of any message.
2. A *reference schema* is a NIEM-conforming XSD representation of a reference model.  It is composed from a set of *reference schema documents*.  We are not supporting other schema langauges (e.g. JSON Schema) as a NIEM modeling formalism, so we do not need any other sort of reference schema.  (We talk about "CMF models", not "CMF schemas".)
3. A *message model* defines the content of a message.  These models include cardinality and datatype facet constraints to precisely specify the mandatory and optional content of conforming messages.  A message model is created by a message designer, who chooses to reuse a subset of data elements in a reference model, and then extends those with data elements he creates for this particular message model.
4. A *message schema* is a NIEM-conforming XSD representation of a message model.  It is composed from a set of *message schema documents*.  Some of these are *subset schema documents*, each a subset of the reference schema document for that namespace.  Some are *extension schema documents*, containing the data elements created by the message designer.

The proposal also depends on revising the NDR to have different conformance targets for reference schema documents and message schema documents.  (It may also be necessary to distinguish between subset schema documents and extension schema documents; TBD.)

I don't think the proposal absolutely requires tool support; one could hand-jam all the required message schema documents.  But it sure will go over a lot better with tools.

**Examples:**  I have contributed a directory containing a reference schema and several message schemas, so if you want to jump over there now and look at example messages and schemas, you will find:

* referenceSchema – the reference schema for the five message schemas
* augmentation – a message schema showing complex content augmented with elements
* augWithAttribute – a message schema showing simple content augmented with attributes
* mdObjectOnComplex – a message schema showing complex content augmented with an element that holds metadata
* mdAttributeOnBoth – a message schema showing simple and complex content augmented with an attribute that holds metadata
* mdObjectOnSimple – a message schema showing simple content augmented with an element that holds metadata

**Advantages of adopting this proposal in NIEM 6:**

1. In NIEM 5, there are two ways to add an object property to complex content:  by augmentation, or via a metadata attribute.
   In NIEM 6, there is only one way:  augmentation
2. In NIEM 5, there is no way to add an attribute to complex or simple content (hence the ugly ISM hack).
   In NIEM 6, attribute augmentations are supported on complex and simple content.
3. In NIEM 5, simple content can only be augmented by elements derived from `structures:MetadataType`,
   In NIEM 6, simple content can be augmented by ordinary object or association elements.
4. In NIEM 5, augmentation element cardinality cannot always be enforced by XML schema validation.
   In NIEM 6, it can.
5. In NIEM 5, an object may contain several augmentation container elements.
   In NIEM 6 there will only be one.
6. In NIEM 5 we expend much effort in defining and explaining the metadata mechanism.
   In NIEM 6, metadata is just another augmentation property.
7. In NIEM 5, a property that modifies the relationship (instead of the parent element) can only be accomplished through the`structures:relationshipMetadata`attribute. 
   In NIEM 6, any property or type can be defined as a relationship property in CMF or through XSD appinfo.
8. In NIEM 5, an object added via metadata attribute may be an object or a container of objects.
   In NIEM 6, it is always an object.
9. In NIEM 5, a metadata attribute can point to any metadata object (i.e. one derived from `structures:MetadataType`.
   In NIEM 6, the message designer can specify exactly what kind of object containing metadata is permitted.
10. In NIEM 5, a metadata object can never modify its parent element.  We have discussed schemes for allowing metadata object to sometimes modify their parents.
      In NIEM 6 these two cases are clearly distinguished by the name of the ID attribute on the object that is metadata.

**Wildcard attribute references**

Here's what's new.  Suppose I am designing a message like this:

```
<my:Message>
  <nc:PersonEducation>
    <nc:EducationDescriptionText>Ph.D.</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
    <nc:EducationAugmentation>
      <em:EducationMinorText>Computer Science</em:EducationMinorText>
      <j:EducationTotalYearsText my:categoryMetadataRef="md01">9</j:EducationTotalYearsText>
      <nc:CommentText>That is a long time!</nc:CommentText>
    </nc:EducationAugmentation>
  </nc:PersonEducation>
</my:Message>
```

And suppose I have an object containing metadata, like this:

```
<my:CategoryMetadata>
  <my:CategoryCode>FOO</my:CategoryCode>
  <my:CategorySourceText>Dr. Scott</my:CategorySourceText>
  <nc:Date>2023-02-15</nc:Date>
</my:CategoryMetadata>
```

Different people can apply a different category code at different times, so this has to be an object; it can't be three independent data properties.  Now suppose I want to apply that metadata to an element with simple content, like `nc:EducationTotalYearsText`.  The resulting message would look like this:

```
<my:Message">
  <nc:PersonEducation>
    <nc:EducationDescriptionText>Ph.D.</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
    <nc:EducationAugmentation>
      <em:EducationMinorText>Computer Science</em:EducationMinorText>
      <j:EducationTotalYearsText my:categoryMetadataRef="md01">9</j:EducationTotalYearsText>
      <nc:CommentText>That is a long time!</nc:CommentText>
    </nc:EducationAugmentation>
  </nc:PersonEducation>
  <my:CategoryMetadata structures:augID="md01">
    <my:CategoryCode>BAR</my:CategoryCode>
    <my:CategorySourceText>Dr. Scott</my:CategorySourceText>
    <nc:Date>2023-02-15</nc:Date>
  </my:CategoryMetadata>
</my:Message>
```

Two new attributes make this work.  `@structures:augID` is an xs:ID value for an element that does not modify its parent.  `@my:categoryMetadataRef` is an xs:IDREFS value holding references to `my:CategoryMetadata` elements; it is created when the message designer says "I want to augment `j:EducationTotalYearsText` with `my:CategoryMetadata`".  The subset schema document for the augmented element then contains a local type definition, as follows:

```
<xs:element name="EducationTotalYearsText" nillable="true">
  <xs:complexType>
    <xs:complexContent>
      <xs:extension base="nc:TextType">
        <xs:attribute ref="my:categoryMetadataRef"/>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
</xs:element>
```

A local type definition is OK in a message schema, because message schemas aren't intended to define data elements for reuse.  (A local type is better than inventing a new global type declaration, because I suspect that would expose us to some `xsi:type` weirdness better avoided.)

The message designer might also want to apply different`my:CategoryMetadata` to the entire message.  (It's his message, so he doesn't need augmentation for that.)  The message might then look like:

```
<my:Message">
  <nc:PersonEducation>
    <nc:EducationDescriptionText>Ph.D.</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
    <nc:EducationAugmentation>
      <em:EducationMinorText>Computer Science</em:EducationMinorText>
      <j:EducationTotalYearsText my:categoryMetadataRef="md01">9</j:EducationTotalYearsText>
      <nc:CommentText>That is a long time!</nc:CommentText>
    </nc:EducationAugmentation>
  </nc:PersonEducation>
  <my:CategoryMetadata structures:augID="md01">
    <my:CategoryCode>BAR</my:CategoryCode>
    <my:CategorySourceText>Dr. Scott</my:CategorySourceText>
    <nc:Date>2023-02-15</nc:Date>
  </my:CategoryMetadata>
  <my:CategoryMetadata>
    <my:CategoryCode>FOO</my:CategoryCode>
    <my:CategorySourceText>Dr. Scott</my:CategorySourceText>
    <nc:Date>2023-02-18</nc:Date>
  </my:CategoryMetadata>  
</my:Message>
```

And if for some reason he wants to apply the same `my:CategoryMetadata` to the message and the `j:EducationTotalYearsText`, the message would look like:

```
<my:Message">
  <nc:PersonEducation>
    <nc:EducationDescriptionText>Ph.D.</nc:EducationDescriptionText>
    <nc:EducationInProgressIndicator>false</nc:EducationInProgressIndicator>
    <nc:EducationAugmentation>
      <em:EducationMinorText>Computer Science</em:EducationMinorText>
      <j:EducationTotalYearsText my:categoryMetadataRef="md01">9</j:EducationTotalYearsText>
      <nc:CommentText>That is a long time!</nc:CommentText>
    </nc:EducationAugmentation>
  </nc:PersonEducation>
  <my:CategoryMetadata s:id="md01">
    <my:CategoryCode>FOO</my:CategoryCode>
    <my:CategorySourceText>Dr. Scott</my:CategorySourceText>
    <nc:Date>2023-02-18</nc:Date>
  </my:CategoryMetadata>  
</my:Message>
```

**Conclusion:**

* It's simpler.  One way to do a thing, not two.  No need to define and explain the metadata mechanism.
* It's more complete:  You can augment simple and complex content with attributes and elements.  Can't do all that in NIEM 5.



Author:  Dr. Scott Renner
Date:  2023-02-19
