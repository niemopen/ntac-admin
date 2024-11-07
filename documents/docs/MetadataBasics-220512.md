# Metadata: Back to Basics

When you find yourself up to your ass in alligators, it is helpful to recall that the goal was to drain the swamp.  With that in mind:  Why does NIEM have a metadata mechanism, anyway?

**Because metadata is special?**

Is it because there is something special about "data about data", something that cannot be handled in the ordinary way by NIEM types, elements, and attributes?  I do not believe this is true.  Conceptually, the distinction between data and metadata is entirely a matter of the consumer's perspective.  To the man trying to display the JPEG image, the EXIF field `OriginalDateTime` is metadata.  To the man trying to sort image files by the order in which they were taken, that field is the data his code needs.  "One man's metadata is another man's data."

Hereafter I will use the term *metadata property* to denote a property that consumers will often regard as metadata.  I think we have many such properties in the NIEM model that have nothing to do with `nc:MetadataType`.  For instance, I think many of the properties in `nc:MeasureType` are metadata properties.  (In all following examples, closing tags are sometimes omitted for easier reading.)

```xml
<nc:TemperatureMeasure>
  <nc:MeasureDecimalValue>98.6
  <nc:MeasureErrorValue>0.1
  <unece:TemperatureUnitCode>FAH
```

I see two metadata properties, each saying something about the measured value.  These are, of course, ordinary properties in the NIEM model; they have nothing to do with NIEM's metadata mechanism.

Can anyone explain the difference in the meaning of these two NIEM elements?

```xml
<exch:CrashDriverInfo>                   <exch:CrashDriverInfo s:metadata="m1">
  <nc:ReportedDate>2022-04-05              <nc:Metadata s:id="m1">
  <nc:ReportingOrganizationText>DOT          <nc:ReportedDate>2022-04-05
                                               <nc:ReportingOrganizationText>DOT
```

If there is something special about "data about data", something that *requires* the metadata mechanism for proper semantics, then these two `CrashDriverInfo` elements should mean something different.  I have no idea what that difference might be.  They seem to me exactly the same.

Relationship metadata is hard to understand, but easy to represent in an ordinary NIEM model, by creating an object for the relationship.  We've had associations in NIEM since at least v2.0.  Relationship objects are just a special simplified form of an association.  So, for instance, I think these two examples mean the same thing.  (In examples, "rmd'" stands for "relationshipMetadata".)

```xml
<Person>                                 <Person>
  <Name>Harry Potter</Name>                <Name>Harry Potter</Name>
  <OwnsItemRelationshp>                    <OwnsItem s:ref="cloak" s:rmd="p1"/>
    <OwnsItem s:ref="cloak"/>            <Metadata s:id="p1"
    <Private>true                          <Private>true
```

Why does NIEM have a metadata mechanism?  It's not because NIEM can't handle metadata without it.

**Because metadata properties are often added to an existing model?**

It is in the nature of metadata, I believe, that it often comes as an afterthought to an existing data model.  If the metadata properties were important from the perspective of the original modeler, he would have put those properties into his model.

Adding a property to an existing class in CMF is not hard.  You just have to keep track of the namespace that "owns" the addition.  But adding a property to an existing type in NIEM XSD is tricky.  The schema document is the configuration item.  You can't change it without publishing a new version.  So adding a property to an existing type requires something special.  In NIEM 3.0 and after, that something special is an *augmentation*.  It works pretty well.  It would work just fine for adding metadata properties.

Why does NIEM have a metadata mechanism? It's not because you can't add properties to existing complex content without it.

**Because adding metadata as an ordinary property makes a complex model?**

It is also in the nature of metadata that it could be applied to almost any property in a model.  Consider `nc:ReportedDate`.  That could be applied to almost any NIEM property, couldn't it?  Now imagine a NIEM model in CMF or XSD that explicitly includes `nc:ReportedDate` on every type.  Ugh.  The metadata mechanism avoids this ugliness.

On the other hand, you could add `nc:ReportedDate` to every complex content type as an augmentation to `structures:ObjectType` and `structures:AssociationType`.  So there is another alternative to metadata complexity doom.

Why does NIEM have a metadata mechanism?  It's not because you'll have horribly complex models without it.

**Because adding metadata as an ordinary property reduces interoperability?**

A message with metadata and a message without metadata are not the same.  The schema is different, and the runtime data is different.  So if I add metadata to the `FooMessage` format, you have to arrange to process `NewFooMessage` messages, even if you don't care about the metadata.

In general this is true whether I added metadata as ordinary properties, or via the metadata mechanism.  In either case you need a new schema, and you need to ignore parts of the new runtime messages.  Most of the time the metadata mechanism doesn't make a big difference to the extra work you must do.

There *is* a big difference when relationship metadata is introduced.  Here's the example of relationship metadata again, with and without the metadata mechanism:

```
<Person>                                 <Person>
  <Name>Harry Potter</Name>                <Name>Harry Potter</Name>
  <OwnsItemRelationshp>                    <OwnsItem s:ref="cloak" s:rmd="p1"/>
    <OwnsItem s:ref="cloak"/>            <Metadata s:id="p1"
    <Private>true                          <Private>true
```

If I add relationship metadata as an ordinary property (*example on the left*), then you have to deal with a *completely new element.*  That's no longer a simple matter of using a new schema and ignoring content you don't need.  You'll have to change your application code to understand the new element.  Or perhaps transform the incoming data before parsing.  Either way is a lot of work for you.  You don't have to do that work if I add relationship metadata via the metadata mechanism (*example on the right*).  Less work == better interoperability.

> NOTE:  this section depends on the RDF-star and JSON-LD-star interpretations of NIEM data.  I don't think we can have a relationship metadata mechanism without them.

Why does NIEM have a metadata mechanism?  One reason is that it improves interoperability when relationship metadata must be added to an existing message format.

**Because it's necessary for metadata properties on simple content?**

Simple content is a literal value.  You can't put a property on a literal.  Only objects can have properties.  So our choices are (1) create a new object property, or (2) use the metadata mechanism.  Examples:

```
<PersonGivenNameObject>                  <PersonGivenName s:metadata="p1">Harry      
  <PersonGivenName>Harry                 <Metadata s:id="p1">
  <Private>true                             <Private>true
```

Deja vu all over again!  Adding metadata as an ordinary property looks just like relationship metadata.  Except it's even uglier.

I would love to say "no metadata on simple content".  But we're all pretty sure that's somehow a bad idea, even if no one has yet produced an example of badness.  So...

Why does NIEM have a metadata mechanism?  One reason is that so we can add metadata to simple content.

**So, is the juice worth the squeeze?**

We are now in a good position to decide if draining the swamp is worth wrangling all these alligators.  Why does NIEM need a metadata mechanism?  It's not because

* NIEM can't handle metadata without it.
* You can't add properties to existing complex content without it
* You'll have horribly complex models without it

But the metadata mechanism does:

* Improve interoperability when relationship metadata is added to an existing message format
* Allow adding metadata properties to simple content

Now we can either whoop all the alligators or climb back out of the swamp.



Author:  Dr. Scott Renner | MITRE | sar@mitre.org | DRAFT | 12 May 2022