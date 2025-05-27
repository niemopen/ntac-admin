### Revisiting URIs and IDs and REFs (oh my!)

This note revisits [an earlier note from 2023](ReferenceAtts-230630.md), which discussed a user request to make reference attributes (`id`, `ref`, and `uri`) schema-valid on some element declarations and not on others. 

URIs, IDs, and REFs make NIEM XML a lot harder to consume. Consider a message like this:

```
<my:Message>
  <my:Object s:uri="#r1" xsi:nil="true"/>
  <my:Object s:ref="r1" xsi:nil="true"/>
  <my:Object s:id="r1">
    <my:Property>FOO</my:Property>
  </my:Object>  
</my:Message>
```

You must be prepared to remember the URLs and IDs and REFs as you parse the XML, then link everything up after parsing is done.  Blech.

NIEM 6 allows a message designer to constrain the kind of references that are allowed on classes and object properties, like this:

```
<Class structures:id="my.ObjectType">
  <Name>Test2Type</Name>
  <Namespace structures:ref="test" xsi:nil="true"/>
  <DocumentationText>Test2Type doc string</DocumentationText>
  <ReferenceCode>ANY</ReferenceCode>
----------
<xs:complexType name="ObjectType" appinfo:referenceCode="ANY">
```

What constraints will message designers need? I want to be able to assert any of these:

1. Reference can be any IDREF or URI
2. Reference can be any URI
3. Reference must be to an object in this message
4. Reference must be a relative URI
5. Reference must be an IDREF
6. No reference allowed, object must be inline

Codes for these might be: `ANY, ANYURI, INTERNAL, RELURI, IDREF, NONE`

What is the right default in NIEM 6?  Well, in NIEM 5 the default is effectively `ANY`, because unless the message designer takes the trouble to subset the structures namespace, any object can have any reference.  But I'd wager that most consuming applications are not looking for REFs and URIs to link objects together.  So I think the right default for NIEM 6 is `NONE`.  If you want object references in your messages, you must say so.

