**The question**

 Should we remove role properties (e.g. `RoleOfPerson`) from the model and achieve the same effect with `structures:uri` attributes?  Let's work through an example of Christina's idea from 9 Feb.

**An example:  One person, three elements in CrashDriver, with role properties**

We can use our trusty CrashDriver example,  I've created a simplified CrashDriver to focus on role properties.  You'll find all the messages and schemas in the [RoleOf](https://github.com/niemopen/ntac-admin/blob/main/examples/src/RoleOf) example in the repo.  

The important thing about this message is that the person driving the crashed vehicle, the person injured in the crash, and the person charged as a result of the crash *is the same person*.  We want our message to reflect that fact.  Here's how we do that in NIEM 5 using `RoleOfPerson `elements:

```
EXAMPLE #1
----------
01  <exch:CrashDriverInfo>
02    <j:Crash>
03      <j:CrashVehicle>
04        <j:CrashDriver>
05          <nc:RoleOfPerson structures:ref="P01"/>
06          <j:DriverLicense>
07            <j:DriverLicenseCardIdentification>
08              <nc:IdentificationID>A1234567</nc:IdentificationID>
09            </j:DriverLicenseCardIdentification>
10          </j:DriverLicense>
11        </j:CrashDriver>
12      </j:CrashVehicle>
13      <j:CrashPerson>
14        <nc:RoleOfPerson structures:ref="P01" xsi:nil="true"/>
15        <j:CrashPersonInjury>
16          <nc:InjuryDescriptionText>Broken Arm</nc:InjuryDescriptionText>
17        </j:CrashPersonInjury>
18      </j:CrashPerson>
19    </j:Crash>
20    <j:Charge structures:id="CH01">
21      <j:ChargeDescriptionText>Furious Driving</j:ChargeDescriptionText>
22      <j:ChargeFelonyIndicator>false</j:ChargeFelonyIndicator>
23    </j:Charge>
24    <j:PersonChargeAssociation>
25      <nc:Person structures:id="P01">
26        <nc:PersonBirthDate>
27          <nc:Date>1890-05-04</nc:Date>
28        </nc:PersonBirthDate>
29        <nc:PersonName nc:personNameCommentText="copied">
30          <nc:PersonGivenName>Peter</nc:PersonGivenName>
31          <nc:PersonMiddleName>Death</nc:PersonMiddleName>
32          <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
33          <nc:PersonSurName>Wimsey</nc:PersonSurName>
34        </nc:PersonName>
35      </nc:Person>
36      <j:Charge structures:ref="CH01" xsi:nil="true"/>
37    </j:PersonChargeAssociation>
38  </exch:CrashDriverInfo>
```

The `CrashDriver` element on line 4, the `CrashPerson` element on line 13, and the `Person` element on line 25 are all describing one real-world entity.  We ought to see that reflected in the equivalent RDF; we ought to get a single resource combining the properties from all three elements, which ought to look like this:

```
EXAMPLE #2
----------
_:P01 a j:CrashDriverType, j:CrashPersonType, nc:PersonType .
_:P01 nc:DriverLicense _:n05 .
_:P01 j:CrashPersonInjury _:n07 .
_:P01 nc:PersonBirthDate _:n09 .
_:P01 nc:PersonName _:n10 .
```

The NIEM 5 NDR does not define special RDF entailment for role properties, so at present we don't get the correct RDF.  If we decide to retain role properties in NIEM 6, we'll need to write some (fairly complicated) new NDR to treat them the same way we treat augmentation elements:  as a sack of properties that belong to the parent, having no existence of its own.  But perhaps we do not need to do that…

**One person, three elements, using structures:uri**

We can get the correct RDF by eliminating role properties and using `structures:uri` instead.  With this approach, the message looks like:

```
EXAMPLE #3
----------
01  <exch:CrashDriverInfo
02    <j:Crash>
03      <j:CrashVehicle>
04        <j:CrashDriver structures:uri="P01">
05          <j:DriverLicense>
06            <j:DriverLicenseCardIdentification>
07              <nc:IdentificationID>A1234567</nc:IdentificationID>
08            </j:DriverLicenseCardIdentification>
09          </j:DriverLicense>
10        </j:CrashDriver>
11      </j:CrashVehicle>
12      <j:CrashPerson structures:uri="P01">
13        <j:CrashPersonInjury>
14          <nc:InjuryDescriptionText>Broken Arm</nc:InjuryDescriptionText>
15        </j:CrashPersonInjury>
16      </j:CrashPerson>
17    </j:Crash>
18    <j:Charge structures:id="CH01">
19      <j:ChargeDescriptionText>Furious Driving</j:ChargeDescriptionText>
20      <j:ChargeFelonyIndicator>false</j:ChargeFelonyIndicator>
21    </j:Charge>
22    <j:PersonChargeAssociation>
23      <nc:Person structures:uri="P01">
24        <nc:PersonBirthDate>
25          <nc:Date>1890-05-04</nc:Date>
26        </nc:PersonBirthDate>
27        <nc:PersonName nc:personNameCommentText="copied">
28          <nc:PersonGivenName>Peter</nc:PersonGivenName>
29          <nc:PersonMiddleName>Death</nc:PersonMiddleName>
30          <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
31          <nc:PersonSurName>Wimsey</nc:PersonSurName>
32        </nc:PersonName>
33      </nc:Person>
34      <j:Charge structures:ref="CH01" xsi:nil="true"/>
35    </j:PersonChargeAssociation>
36  </exch:CrashDriverInfo>
```

Now the `CrashDriver` element on line 4, the `CrashPerson` element on line 13, and the `Person` element on line 25 all have the same `structures:uri="P01"` attribute, which means they are all describing the same resource.  `CrashDriver` no longer HAS-A `RoleOfPerson`; instead `CrashDriver` IS-A `PersonType` (through inheritance). 

The message is simpler without role properties.  The NIEM model is simpler without role properties.  The NDR is simpler without role properties.  What's not to like?

**Complexities with cardinality constraints**

We just said that `CrashDriver` IS-A `PersonType`.  The XSD for that looks like line 3 below:

```
EXAMPLE #4
----------
01  <xs:complexType name="CrashDriverType">
02    <xs:complexContent>
03      <xs:extension base="nc:PersonType">
04        <xs:sequence>
05          <xs:element ref="j:DriverLicense" minOccurs="0" maxOccurs="unbounded"/>
06        </xs:sequence>
```

Now let's suppose that in our message specification, a `PersonType` always has a `PersonName`.  Boom! our message no longer validates, because the `CrashDriver` element doesn't contain a `PersonName` element.  We don't want to fix that by inserting a `PersonName` element – the whole point of this exercise is to avoid repeating the same `PersonName` data throughout the message!

We therefore must change the schema so that `CrashDriver`Type doesn't inherit from `PersonType`.  (We can make that work with a clever RDF entailment in the NDR, and maybe some appinfo to document that `CrashDriverType` really IS-A `PersonType` even though it doesn't inherit.)  Now the example message above does validate.  Everyone is happy until we need a message that has a `CrashDriver` but doesn't have a `Charge`.  Where does the `PersonName` element go?  We want something like the following: 

```
EXAMPLE #5
----------
01  <exch:CrashDriverInfo
02    <j:Crash>
03      <j:CrashVehicle>
04        <j:CrashDriver>
05          <nc:PersonBirthDate>
06            <nc:Date>1890-05-04</nc:Date>
07          </nc:PersonBirthDate>
08          <nc:PersonName nc:personNameCommentText="copied">
09            <nc:PersonGivenName>Peter</nc:PersonGivenName>
10            <nc:PersonMiddleName>Death</nc:PersonMiddleName>
11            <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
12            <nc:PersonSurName>Wimsey</nc:PersonSurName>
13          </nc:PersonName>
14          <j:DriverLicense>
15            <j:DriverLicenseCardIdentification>
16              <nc:IdentificationID>A1234567</nc:IdentificationID>
17            </j:DriverLicenseCardIdentification>
18          </j:DriverLicense>
19        </j:CrashDriver>
20      </j:CrashVehicle>
21    </j:Crash>
22  </exch:CrashDriverInfo>
```

But it's not valid because `CrashDriver`Type doesn't inherit from `PersonType`.  Bozhe moi!  Danged if we do and danged if we don't.

There is a way out of the dialemma.  We can make sure the schema has room for `Person` elements at the top level.  The message schema looks like this:

```
EXAMPLE #6
----------
01  <xs:complexType name="CrashDriverInfoType">
02    <xs:complexContent>
03      <xs:extension base="structures:ObjectType">
04        <xs:sequence>
05          <xs:element ref="j:Crash"/>
06          <xs:element ref="j:Charge" minOccurs="0" maxOccurs="unbounded"/>
07          <xs:element ref="j:PersonChargeAssociation" minOccurs="0" maxOccurs="unbounded"/>
08          <xs:element ref="nc:Person" minOccurs="0" maxOccurs="unbounded"/>
09        </xs:sequence>
```

Adding the element on line 8 allows the message to state "these people exist", and allows other elements to refer to them.  The message then looks like this:

```
EXAMPLE #7
----------
01  <exch:CrashDriverInfo
02    <j:Crash>
03      <j:CrashVehicle>
04        <j:CrashDriver structures:uri="P01">
05          <j:DriverLicense>
06            <j:DriverLicenseCardIdentification>
07              <nc:IdentificationID>A1234567</nc:IdentificationID>
08            </j:DriverLicenseCardIdentification>
09          </j:DriverLicense>
10        </j:CrashDriver>
11      </j:CrashVehicle>
12    </j:Crash>
13    <nc:Person structures:uri="P01">
14      <nc:PersonBirthDate>
15        <nc:Date>1890-05-04</nc:Date>
16      </nc:PersonBirthDate>
17      <nc:PersonName nc:personNameCommentText="copied">
18        <nc:PersonGivenName>Peter</nc:PersonGivenName>
19        <nc:PersonMiddleName>Death</nc:PersonMiddleName>
20        <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
21        <nc:PersonSurName>Wimsey</nc:PersonSurName>
22      </nc:PersonName>
23    </nc:Person>
24  </exch:CrashDriverInfo>
```

Example #3 and example #7 are both valid with this approach, so we pass GO and collect $200.

**How to replace a role property**

These are the steps for replacing `RoleOfPerson` in `CrashDriverType`.  They can be generalized to replacing any `RoleOfFOO` property in an `ExtendedType`, or to creating a brand new `ExtendedType` that would have a role property in NIEM 5.

1. `RoleOfPerson` is a `PersonType`.  First, confirm that `CrashDriverType` is properly thought of as an extension of `PersonType` – that it is a subclass, a special kind of `PersonType` with all the same properties, and also some *other* properties which ordinary `PersonType` objects do *not* have.  If this isn't true, then you don't want a role property and you don't want to replace a role property with `structures:uri`.  Fix the broken model instead.

2. If you are absolutely certain that `PersonType` will never have any mandatory content in any IEPD, then you can just write

   ```
   <xs:complexType name="CrashDriverType">
     <xs:complexContent>
       <xs:extension base="nc:PersonType">
         <xs:sequence>
           <xs:element ref="j:DriverLicense" minOccurs="0" maxOccurs="unbounded"/>
         </xs:sequence>
   ```

   omitting the role property, and you're done.  But I think that you will never have that certainty.  It's definitely not true of `PersonType`.

3. Instead, define `CrashPersonType` without inheritance, omitting the role property, and perhaps including some appinfo to help users (and their tools) understand what's going on,

   ```
   <xs:complexType name="CrashDriverType" appinfo:isA="nc:PersonType">
     <xs:complexContent>
       <xs:extension base="structures:ObjectType">
         <xs:sequence>
           <xs:element ref="j:DriverLicense" minOccurs="0" maxOccurs="unbounded"/>
         </xs:sequence>
   ```

   BTW, we could write a new NDR to say that since `CrashDriverType` has `appinfo:isA="nc:PersonType"`, every `CrashDriver` element must have `structures:uri="`*foo*`"`,  where *foo* is an element of `nc:PersonType` or a derived type.

4. Then, whenever you put `CrashDriverType` into a message specification, ask yourself:  Are you *certain* that your message will *always* contain a `Person` matching every `CrashDriver`.  If not, you should include `Person` as an optional, repeatable element at the message level, as shown on line 8 in example #6.


Is that too complicated?  Well, we can always fix role properties, if that seems better and simpler…

**RoleOf properties in NIEM 5.2**

Whatever we decide, we should also look through role properties in the model and make sure they all make sense.  I found 24 role properties in NIEM 5.2, and 84 type definitions that include a role property.  I'm pretty sure I found at least one modeling error:  `mo:TargetType` includes `nc:RoleOfPerson`, but a target is not a special kind of person.  There could be other errors.



Author:  Scott Renner
Date:  2023-05-04
