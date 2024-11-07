### URIs and IDs and REFs, Oh My!

This note originates in a user request to make reference attributes (`id`, `ref`, and `uri`) schema-valid on some element declarations and not on others.  The note concludes that to do this, we need a new conformance target for message schema documents.  There are some interesting examples in between.

URIs, IDs, and REFs make NIEM XML a lot harder to consume.  It's pretty easy to read this into your Java program or whatever:

```
<my:Message>
  <my:Object>
    <my:Property>FOO</my:Property>
  </my:Object>
  <my:Object>
    <my:Property>FOO</my:Property>
  </my:Object>  
</my:Message>
```

But add object references, and things get a lot harder:

```
<my:Message>
  <my:Object s:ref="r1" xsi:nil="true"/>
  <my:Object s:id="r1">
    <my:Property>FOO</my:Property>
  </my:Object>  
</my:Message>
```

Now you have to remember the IDs and REFs as you parse the XML, then link everything up after parsing is done.  Blech.  Oh, and by the way, JAXB doesn't do the right thing with NIEM refs.  (Java binding for NIEM XML is a lot simpler than Java binding for arbitrary XML, and so there may come a day when there is a NIEM-Java binding tool.  But it is not this day.)

In NIEM 5 you can entirely remove object references from your message specification by subsetting the structures namespace.  But the Universal C2 Language (UC2) guys don't want to do that.  They've asked for a way to make the reference attributes (`id`, `ref`, and `uri`) schema-valid on some elements, and not on others.  Seems  reasonable.

NIEM already has something *almost* like that.  If you have an element declaration and type definition like this

```
<xs:element name="Thing" type="my:ThingType"/>
<xs:complexType name="ThingType">
  <xs:complexContent>
    <xs:extension base="s:ObjectType">
      <xs:sequence>
        <xs:element ref="my:Property"/>
```

then your message can't have a reference to a `Thing`  element, like this

```
<my:Thing s:ref="foo" xsi:nil="true"/>
```

because a `Thing` element must have content and it isn't nillable.  So that's kind of like saying "this element must be inline".  This is why I put a `ReferenceableIndicator` property into CMF; it's true for nillable elements, false otherwise.  But that doesn't satisfy the user request, because (a) it doesn't work for complex content with no mandatory elements, (b) it doesn't work for simple content at all, and (c) when it does work, the reference attributes are still schema-valid,

Like a lot of things these days, satisfying that user request is pretty easy to do in CMF and a whole lot of work in XSD.  In CMF you just put `ReferenceableIndicator=false` on classes and properties as needed, and then generate some validation XSD (which doesn't have to follow NDRs), or validation JSON Schema, or whatever.  Boom, done.

Not nearly so simple if you're using XSD as your modeling formalism.  You don't know in advance which elements should have the reference attributes, which means they must be part of every type definition in a reference schema.  But in the message schema – the XSD that defines a particular message format – you need a way to allow reference attributes on some element declarations and not on others.  Here's how that can work.

1. The structures namespace defines the reference attributes and a reference attribute group.  It no longer defines `ObjectType` or `AssociationType`.  (It does define `AugmentationType`, but that's not important here.)

   ```
   <xs:attribute name="id" type="xs:ID"/>
   <xs:attribute name="ref" type="xs:IDREF"/>
   <xs:attribute name="uri" type="xs:anyURI"/>
   <xs:attributeGroup name="ReferenceAttributeGroup">
     <xs:attribute ref="id"/> ,,,
   ```

2. In a reference or subset schema, the new inheritance base types `nc:ObjectType` and `nc:AssociationType` have an attribute wildcard.  (We need that for attribute augmentations anyway; we can use it for the reference attributes, no extra charge.)  Since they have this attribute wildcard, there's no need for the old `SimpleObjectAttributeGroup`.

   ```
   <xs:complexType name="ObjectType">
     <xs:sequence>
       <xs:element ref="nc:ObjectAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
     </xs:sequence>
     <xs:anyAttribute processContents="lax"/>
   </xs:complexType>
   ```

3. Every NIEM complex type ultimately inherits from one of those `nc` base types

   ```
   <xs:complexType name="EducationType">
     <xs:complexContent>
       <xs:extension base="nc:ObjectType">
         <xs:sequence>
           <xs:element ref="nc:EducationDescriptionText"...
         </xs:sequence>
       </xs:extension>
     </xs:complexContent>
   </xs:complexType>
   ```

4. And so every element declaration in a reference schema says it *can* have reference attributes.  But it *may not* in a particular message, depending on the message designer

   ```
   <xs:element name="PersonEducation" type="nc:EducationType" nillable="true"/>
   ```

5. In a message schema, the attribute wildcards are removed from the two `nc` base types:

   ```
   <xs:complexType name="ObjectType">
     <xs:sequence>
       <xs:element ref="nc:ObjectAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
     </xs:sequence>
   </xs:complexType>
   ```

6. All the other complex type definitions get the reference attributes at the discretion of the message designer.  For example, if the designer wants reference attributes on elements of type `nc:EducationType` by default, he writes:

   ```
   <xs:complexType name="EducationType">
     <xs:complexContent>
       <xs:extension base="nc:ObjectType">
         <xs:sequence>
           <xs:element ref="nc:EducationDescriptionText"/>
         <xs:sequence>
         <xs:attributeGroup ref="s:ReferenceAttributeGroup"/>
       </xs:extension> ...
   ```

   If the message designer is satisfied with the default for a particular element, then the element declaration uses the global type.  In this example, if he wants reference attributes on`nc:PersonEducation`, he writes:

   ```
   <xs:element name="PersonEducation" type="nc:EducationType"/>
   ```

   If the message designer is not satisfied with the default, he uses a local type definition.  In this example, if the designer wants `PersonEducation` to always be inline, he writes:

   ```
   <xs:element name="PersonEducation">
     <xs:complexType>
       <xs:complexContent>
         <xs:extension base="nc:ObjectType">
           <xs:sequence>
             <xs:element ref="nc:EducationDescriptionText"/>
           <xs:sequence>
         </xs:extension>
       </xs:complexContent>
     </xs:complexType>
   </xs:element>
   ```

7. Suppose the message designer wants elements of type `nc:EducationType` to be inline by default.  (I think this is the common case.)  Then he omits the attribute group from the type definition:

   ```
   <xs:complexType name="EducationType">
     <xs:complexContent>
       <xs:extension base="nc:ObjectType">
         <xs:sequence>
           <xs:element ref="nc:EducationDescriptionText"/>
         <xs:sequence>
       </xs:extension> ...
   ```

   Again, if he is satisfied with the default, he uses the global type.  So now if he wants `PersonEducation` to be always inline, he writes

   ```
   <xs:element name="PersonEducation" type="nc:EducationType"/>
   ```

   And if he wants `PersonEducation` to have the reference attributes, he uses a local type:

   ```
   <xs:element name="PersonEducation">
     <xs:complexType>
       <xs:complexContent>
         <xs:extension base="nc:EducationType">
           <xs:attributeGroup ref="s:ReferenceAttributeGroup"/>
         </xs:extension>
       </xs:complexContent>
     </xs:complexType>
   </xs:element>
   ```

7. The same trick allows you to put reference attributes on elements with simple content.  The proxy types in a reference schema also have an attribute wildcard.

   ```
   <xs:complexType name="boolean">
     <xs:simpleContent>
       <xs:extension base="xs:boolean">
         <xs:anyAttribute processContents="lax"/>
       </xs:extension>
     </xs:simpleContent>
   </xs:complexType>
   ```

8. And so the reference schema says that any of our simple-content elements *can* have reference attributes, but *may not*, depending on the message designer

   ```
   <xs:element name="EducationInProgressIndicator" type="xs-proxy:boolean" nillable="true"/>
   ```

9. In the message schema, the proxy types lose the attribute wildcard.  Which means that in a message schema we can dispense with the proxy types entirely, and just use XSD datatypes.  This means that simple-content elements do not have reference attributes by default.  If the message designer is happy with that (by far the common case), the message schema contains

   ```
   <xs:element name="EducationInProgressIndicator" type="xs:boolean"/>
   ```

10. But if he absolutely needs references to simple content, then he uses a local type definition, like this:

    ```
    <xs:element name="EducationInProgressIndicator">
      <xs:complexType>
        <xs:simpleContent>
          <xs:extension base="xs:boolean">
            <xs:attributeGroup ref="s:ReferenceAttributeGroup"/> ...
    ```

That is quite complicated when compared to `ReferenceableIndcator=false` in CMF.  But we already needed all that complexity in order to do attribute augmentations, and to support different cardinality constraints on elements that have the same type in the reference schema.  (For example, `nc:PersonPreferredName` and `nc:CommentText` are both of `nc:TextType`, but perhaps in your message specification you want `@xml:lang` on only one of them.)

It's going to be a moderate PITA to accomodate all this in CMFTool.  But eventually a message designer will be able to write a CMF file with`ReferencableIndicator` on `nc:ObjectType` and `nc:AssociationType` to set the default for all types, override that default by putting `ReferenceableIndicator` on particular type definitions, and then override that default with `ReferenceableIndicator` on particular element declarations.  CMFTool will then be able to generate a message schema in XSD from CMF, and turn that XSD back into CMF.

The new `MessageSchemaDocument` conformance target?  Well, local type definitions aren't allowed in reference or extension schemas, and we probably break a dozen other rules, so I don't think we have a choice if we want to use XSD as a modeling formalism for particular message formats.



Author: Scott Renner
Date: 2023-06-30