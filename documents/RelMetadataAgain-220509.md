# Relationship metadata, revisited

You may remember our problem with relationship metadata:  a really ugly JSON-LD representation that could not possibly be processed as plain JSON.  GO to JAIL, do not pass GO, do not collect $200.  So we decided to get rid of relationship metadata, and instead force the message designer to create a relationship object.  That's ugly, but not as ugly as losing all the JSON developers.

 I'm looking at the new RDF-star and JSON-LD-star specifications.  I think they will work for relationship metadata.  For example, we might have a message like this:

```XML
<Item s:id="map"><Name>Marauder’s Map</Name></Item>
<Item s:id="cloak"><Name>Cloak of Invisibility</Name></Item>
<Item s:id="bird"><Name>Hegwid</Name></Item>	
<Person>
  <Name>Harry Potter</Name>
  <OwnsItem s:ref="map" s:metadata="priv"/>
  <OwnsItem s:ref="cloak" s:relationshipMetadata="priv"/>
  <OwnsItem s:ref="bird"/>
 <Metadata s:id="priv">
   <Private>true</Private>
 </Metadata>
```

In Harry Potter lore, the *Cloak of Invisibility* is a well-known artifact.  Everybody knows it exists; what's private is the fact that Harry owns it.  So for this `OwnsItem` element, we must use relationship metadata.  On the other hand, for our purposes we will assume the existence of the *Marauder's Map* is not well known.  For this, plain metadata works for this.  (It doesn't matter whether we put the metadata attribute on the `Item` or the `OwnsItem` element, because they refer to the same object.)

RDF-star extends the RDF model with the concept of a *quoted triple*, which can be the subject of another triple.  Using RDF-star syntax, the relationship metadata is expressed like this:

```
_:n2 ns:OwnsItem msg:cloak {| pr:Private "true" |}
```

And who cares?  Not me, until I saw how that would be represented in JSON-LD-star:

```
{
  "ns:OwnsItem": {
    "@id": "#cloak",
    "@annotation": {
      "pr:Private": "true"
    }
  }
}
```

That can be processed as plain JSON!  So we pass GO, and collect $200!

I played around with a SPARQL processor and a slightly larger dataset, and was able to write queries that exclude data marked private.  I can ask for a list of all known items

```
SELECT ?z
WHERE {
  ?x ns:Item ?y .
  ?y ns:Name ?z .
  MINUS { ?y pr:Private "true" }
```

and I get

```
---------------------------
| z                       |
===========================
| "Cloak of Invisibility" |
| "Hegwid"                |
| "Crookshanks"           |
---------------------------
```

The list doesn't include the *Marauder's Map*, because that object is marked private.  If I ask for a list of who owns what

```
SELECT ?n ?z
WHERE {
  ?x ns:OwnsItem ?y .
  ?x ns:Name ?n .
  ?y ns:Name ?z .
  MINUS { ?y pr:Private "true" }
  BIND (TRIPLE (?x,ns:OwnsItem,?y) as ?t)
  MINUS { ?t pr:Private "true" }
```

then I get

```
----------------------------------
| n              | z             |
==================================
| "Harry Potter" | "Hegwid"      |
| "Ron Weasley"  | "Crookshanks" |
----------------------------------
```

That list doesn't include the *Marauder's Map*, because that object is marked private.  And it doesn't include the *Cloak of Invisibility*, because that relationship is marked private.  There's more to attribute-based access control than that, of course, but I think this shows that our metadata mechanism will support ABAC.  Which is enough for now.

# Metadata object or container?

Sometimes a metadata attribute points to a sack of properties, like an augmentation.

```
<my:Thing s:metadata="m1">300.5</my:Value>
<my:Metadata s:id="m1">
  <nc:MeasureErrorValue>0.35</nc:MeasureErrorValue>
  <nc:MeasureUnitText>pounds</nc:MeasureUnitText>
</my:Metadata>
```

Sometimes a metadata attribute points to an object, something that could be repeated

```
<my:Thing s:metadata="m1 m2">300.5</my:Value>
<my:Reporting s:id="m1">
  <nc:ReportedDate>2022-03-04</nc:ReportedDate>
  <nc:ReportingPersonText>Fred</nc:ReportingPersonText>
</my:Reporting>
<my:Reporting s:id="m2">
  <nc:ReportedDate>2022-05-22</nc:ReportedDate>
  <nc:ReportingPersonText>Bob</nc:ReportingPersonText>
</my:Reporting>
```

I want to make the message designer tell us which one he wants. I don't know how to do it yet.  But I want something about the target object of a metadata pointer to say whether it's a sack of unrelated properties, or a distinct object with properties of its own.  Because if it's a sack of properties, then I want to treat it as an augmentation object, and just suck those properties into the object with the pointer:

```
_:b1 my:Thing _:b2 .
_:b2 my:ThingLiteralValue "300.5" ;
     nc:MeasureErrorValue "0.35" ;
     nc:MeasureUnitText "pounds" .
```

But if it's a distinct object, possibly repeated, then the object becomes a property.

```
_:b1 my:Thing _:b2 .
_:b2 my:ThingLiteralValue "300.5" ;
     my:Reporting _:b3 ;
     my:Reporting _:b4 .
_:b3 nc:ReportedDate "2022-03-04" ;
     nc:ReportingPersonText "Fred" .
_:b4 nc:ReportedDate "2022-05-22" ;
     nc:ReportingPersonText "Bob" .     
```

Instead of somehow indicating the difference, we could just say that a metadata object is *always* a sack of properties.  Then if you wanted distinct objects, you would have to repeat them inside the sack, like this:

```
<my:Thing s:metadata="m1">300.5</my:Value>
<my:Metadata s:id="m1">
  <my:Reporting>
    <nc:ReportedDate>2022-03-04</nc:ReportedDate>
    <nc:ReportingPersonText>Fred</nc:ReportingPersonText>
  </my:Reporting>
  <my:Reporting>
    <nc:ReportedDate>2022-05-22</nc:ReportedDate>
    <nc:ReportingPersonText>Bob</nc:ReportingPersonText>
  </my:Reporting>
</my:Metadata>
  
```

That might be the way to go.

# Metadata: Some proposals

1.  Metadata properties as ordinary child elements and metadata properties through a metadata attribute pointer should be the same.  So you can do this:

   ```
   <exch:CrashDriverInfo>
     <nc:ReportedDate>2022-04-30
     <nc:ReportingOrganizationText>Virginia DOT
   ```

   or you can do this:

   ```
   <exch:CrassDriverInfo s:metadata="foo">
   . . .
   <exch:Metadata s:id="foo">
     <nc:ReportedDate>2022-04-30
     <nc:ReportingOrganizationText>Virginia DOT
   ```

   and it should mean the same thing and produce the same RDF either way.

2. The message designer needs to tell us when simple content can have a metadata attribute, so we can turn it into an object property via the `FooLiteralValue` trick.

3. The scope of metadata properties is properly the concern of the message designer and the consuming software.  For example, different parts of a message could have different values for the same metadata property, like this:

   ```
   <exch:CrashDriverInfo>
     <nc:ReportedDate>2022-04-30
     <nc:ReportingOrganizationText>Virginia DOT
     <j:Crash>
       <j:CrashPerson>
         <j:CrashPersonInjury>
           <nc:InjuryDescriptionText>Broken Arm
           <j:InjurySeverityCode>3
           <exch:ProvenanceMetadata>
             <nc:ReportedDate>2022-04-15
             <nc:ReportingOrganizationText>Inova
   ```

   What does that mean?  The overall message came from Virginia DOT on 2022-04-30. The injury was reported by Inova on 2022-04-15. And now the message model and message instance have done their job.  *Any interpretation beyond that is the responsibility of the consuming application.* For instance, given the question "when was this broken arm reported?", the consumer is expected to produce the answer "2022-04-15". We also expect the consumer to handle any needed distinction between the provenance of the entire message and the provenance of its parts.



Author: Dr. Scott Renner | MITRE | sar@mitre.org | DRAFT | 10 May 2022
