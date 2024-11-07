# Message extension in NIEM 6

This is a discussion paper for the OASIS NIEMOpen Technical Architecture committee.

One message designer will sometimes wish to make extensions to a message format designed by another.  This paper describes two patterns for message extension and illustrates their implementation in NIEM 6.

Suppose designer Burt creates a format with messages that look like this:

```
<burt:Message>
  <burt:What>Armored Car</burt:What>
  <burt:Where>4QFJ123678</burt:Where>
  <burt:When>2023-02-18T10:15:45</burt:When>
</burt:Message>
```

We'll call that the BURT message format.  (It's a crap-on-a-map message; consumers use it to place icons on a map display. The location format is MGRS, if you care.)  Producing and consuming application developers get the message specification, understand the message schema, and write software that generates and parses BURT messages.  Everyone is happy, until designer Ernie realizes his exchange partners need to know the color of the What.  Now, Ernie *could* create an entirely different kind of message, while still reusing most of Burt's data elements, like this:

```
<ernie:Message>
  <tom:What>Armored Car</tom:What>
  <tom:Where>4QFJ123678</tom:Where>
  <tom:When>2023-02-18T10:15:45</tom:When>
  <ernie:Color>Red</ernie:Color>
</ernie:Message>
```

But that's not a message extension.  When Ernie creates an extension of Burt's message format, the messages look like this instead:

```
<burt:Message>
  <burt:What>Armored Car</burt:What>
  <burt:Where>4QFJ123678</burt:Where>
  <burt:When>2023-02-18T10:15:45</burt:When>
  <burt:MessageAugmentation>
    <ernie:Color>Red</ernie:Color>
  </burt:MessageAugmentation>
</burt:Message>
```

We'll call that the ERNIE message format.  And now some developers get the ERNIE message specification, understand that message schema, and write software to generate and parse ERNIE messages.  Meanwhile, there's still plenty of code that handles BURT messages but doesn't know anything about the ERNIE format.  This is what message extension looks like.  

Here's the question:  Is a ERNIE message also a valid BURT message?  There are two patterns of message extension corresponding to the two answers.

**ERNIE messages are *not* valid BURT messages**

The BURT message format doesn't say anything about the `ernie:Color` element.  Developers of the BURT consumers didn't know anything about the `ernie:Color` element when they wrote their code.  It could mean *anything*.  The only safe choice is to reject the message.

If this is what Burt wants, he writes a reference schema document for his extension data elements that looks like this:

```
<xs:complexType name="MessageType">
  <xs:complexContent>
    <xs:extension base="structures:ObjectType">
      <xs:sequence>
        <xs:element ref="burt:What"/>
        <xs:element ref="burt:Where"/>
        <xs:element ref="burt:When"/>
        <xs:element ref="burt:MessageAugmentation" minOccurs="0"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
<xs:element name="MessageAugmentation" type="structures:AugmentationType"/>
```

Burt wants extensions of his BURT message to be possible, so he puts an augmentation point into his reference schema.  (Or perhaps the augmentation point is required by NDR rules for a reference schema document.)  Then Burt creates a message schema document, which  looks like this:

```
<xs:complexType name="MessageType">
  <xs:complexContent>
    <xs:extension base="structures:ObjectType">
      <xs:sequence>
        <xs:element ref="burt:What"/>
        <xs:element ref="burt:Where"/>
        <xs:element ref="burt:When"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
```

Burt does not need to augment the types he creates.  Burt doesn't want any extension of his message to be valid BURT messages.  So his message schema does not have an augmentation element.

When Ernie generates a message schema from Burt's reference schema, it looks like this:

```
<xs:complexType name="MessageType">
  <xs:complexContent>
    <xs:extension base="structures:ObjectType">
      <xs:sequence>
        <xs:element ref="burt:What"/>
        <xs:element ref="burt:Where"/>
        <xs:element ref="burt:When"/>
        <xs:element ref="burt:MessageAugmentation"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
<xs:element name="MessageAugmentation">
  <xs:complexType>
    <xs:sequence>
      <xs:element ref="ernie:Color"/>
    </xs:sequence>
  </xs:complexType>
</xs:element>
```

And so a ERNIE message is valid against Ernie's message schema, but not valid against Burt's message schema.  QED

**ERNIE messages are not valid BURT messages, but BURT clients process them anyway**

By the way, this is what will happen unless BURT consumers do some sort of message validation.  For instance, if your BURT consumer uses SAX parsing to pull out the fields of interest, it will consume a ERNIE message without a qualm.

**ERNIE messages *are* valid BURT messages**

The other answer is for BURT consumers to treat ERNIE messages as valid, and ignore the parts they do not understand.  A BURT consumer could process a ERNIE message to put an armored car icon on the map, even though it does not know what `ernie:Color` means.

If this is what Burt wants, he must put an augmentation point *with a wildcard* into his *message schema*.  The reference schema looks just the same, but now the message schema looks like this:

```
<xs:complexType name="MessageType">
  <xs:complexContent>
    <xs:extension base="structures:ObjectType">
      <xs:sequence>
        <xs:element ref="burt:What"/>
        <xs:element ref="burt:Where"/>
        <xs:element ref="burt:When"/>
        <xs:element ref="burt:MessageAugmentation" minOccurs="0"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
<xs:element name="MessageAugmentation">
  <xs:complexType>
    <xs:sequence>
      <xs:any processContents="lax"/>
    </xs:sequence>
  </xs:complexType>
</xs:element>
```

And now a ERNIE message is valid against the BURT message schema.  QED.  This pattern is a form of *layered interoperability,* in which all participants understand a useful common base layer of data, and then sub-communities understand special extension layers as needed.  An instance of the pattern is sometimes known as a *loose coupler*.  Cursor-on-Target (CoT) is a well-known example.

Note that Ernie has the burden of ensuring his extension does not change the meaning of Burt's message in any way.  For example, you can imagine something very bad happening if a BURT consumer received an ERNIE message like

```
<burt:NuclearWarNow>
  <burt:LaunchEverything>true</burt:LaunchEverything>
  <burt:NuclearWarNowAugmentation>
    <ernie:JustADrill>true</ernie:JustADrill>
  </burt:NuclearWarNowAugmentation>
</burt:NuclearWarNow>
```

**Extension points don't have to be augmentation elements**

We get the same results if Burt uses `burt:ExtPoint` as the extension point instead of `burt:MessageAugmentation`.  We can probably get a little more help from tools if Burt uses the augmentation mechanism, that's all.

**Message extension and self-describing data**

It sure would be handy if every NIEM message told you the message format to which it conforms.  We can almost make this work through a convention involving the URI of the message element.  For example, with a BURT message beginning

```
<burt:Message 
    xmlns:burt="http://example.com/Burt/1.0/">
```

The URI of the message element is `http://example.com/Burt/1.0/#Message`.   The URI of the message format could then be`http://example.com/Burt/1.0/#Message:Format`.  It's not a big stretch to say that each message format should have a distinct message element.

Except.  That convention won't work with message extension.  The ERNIE message format has the same message element and so would have the same message format URI.  Dang.

NIEM messages could use CTAS to describe their message format.  That would look like this:

```
<burt:Message 
	  xmlns:ct="http://release.niem.gov/niem/conformanceTargets/3.0/"
    xmlns:burt="http://example.com/Burt/1.0/"
    ct:conformanceTargets="http://example.com/Burt/1.0/#Message:Format">
    <burt:What>Armored Car</burt:What>
    <burt:Where>4QFJ123678</burt:Where>
    <burt:When>2023-02-18T10:15:45</burt:When>
 </burt:Message>
```

But then the message designer would have to put the `ct:conformanceTargets` attribute into the message schema.  And that's a lot of extra message data if you're working in the low-bandwidth world.

My crackpot idea for today is to use a reserved namespace prefix in those cases where the message element URI isn't what you want, like this:

```
<burt:Message 
	  xmlns:NIEMFormat="http://example.com/Burt/1.0/#Message:Format"
    xmlns:burt="http://example.com/Burt/1.0/">
    <burt:What>Armored Car</burt:What>
    <burt:Where>4QFJ123678</burt:Where>
    <burt:When>2023-02-18T10:15:45</burt:When>
 </burt:Message>
```

Now there's nothing to add to the message schema.  And that namespace declaration would turn to zero bits in a schema-aware EXI serialization.



Author:  Dr. Scott Renner
Date: 2023-02-20