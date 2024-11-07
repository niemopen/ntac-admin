Turns out that augmentation point elements are a necessary XSD hack.  I tried replacing augmentation points with a single wildcard in structures, like this:

```
<xs:complexType name="ObjectType" abstract="true">
  <xs:sequence>
    <xs:any minOccurs="0" maxOccurs="unbounded" namespace="##other" processContents="lax"/>
  </xs:sequence>
  <xs:attribute ref="structures:augID"/>
  <xs:attribute ref="structures:id"/>
  <xs:attribute ref="structures:ref"/>
  <xs:attribute ref="structures:uri"/>
  <xs:anyAttribute namespace="##other" processContents="strict"/>
</xs:complexType>
```

I expected to get Unambiguous Particle Attribution errors.  But it worked fine for all my examples.  Huh.  Maybe we don't need augmentation points…

Then I tried a new example with inherited types.  I put together a message model in which I augmented `nc:ItemType` with `j:ItemMerchandiseIndicator` and `em:ItemExpirationDate`, `nc:ConveyanceType` with `j:ConveyanceJointlyRegisteredIndicator`, and `nc:VehicleType` with `j:VehicleGarageIndicator`.  The locked-down message schema allows only the content I selected.  It looks like this:

```
  <xs:complexType name="ConveyanceType">
    <xs:complexContent>
      <xs:extension base="nc:ItemType">
        <xs:sequence>
          <xs:element ref="j:ConveyanceJointlyRegisteredIndicator" appinfo:augmentation="true"/>
          <xs:element ref="nc:ConveyanceEngineQuantity"/>
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:complexType name="ItemType">
    <xs:complexContent>
      <xs:extension base="structures:ObjectType">
        <xs:sequence>
          <xs:element ref="j:ItemMerchandiseIndicator" appinfo:augmentation="true"/>
          <xs:element ref="em:ItemExpirationDate" appinfo:augmentation="true"/>
          <xs:element ref="nc:ItemDescriptionText"/>
          <xs:element ref="nc:ItemModelYearDate"/>
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:complexType name="VehicleType">
    <xs:complexContent>
      <xs:extension base="nc:ConveyanceType">
        <xs:sequence>
          <xs:element ref="j:VehicleGarageIndicator" appinfo:augmentation="true"/>
          <xs:element ref="nc:VehicleDoorQuantity"/>
          <xs:element ref="nc:VehicleSeatingQuantity"/>
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
```

and the message looks like this:

```
<my:Message>
  <nc:Vehicle>
    <j:ItemMerchandiseIndicator>false</j:ItemMerchandiseIndicator>
    <em:ItemExpirationDate>
      <nc:YearDate>2022</nc:YearDate>
    </em:ItemExpirationDate>
    <nc:ItemDescriptionText>Large</nc:ItemDescriptionText>
    <nc:ItemModelYearDate>2015</nc:ItemModelYearDate>
    <j:ConveyanceJointlyRegisteredIndicator>true</j:ConveyanceJointlyRegisteredIndicator>
    <nc:ConveyanceEngineQuantity>2</nc:ConveyanceEngineQuantity>
    <j:VehicleGarageIndicator>true</j:VehicleGarageIndicator>
    <nc:VehicleDoorQuantity>4</nc:VehicleDoorQuantity>
    <nc:VehicleSeatingQuantity>8</nc:VehicleSeatingQuantity>
  </nc:Vehicle>
</my:Message>
```

Oops!  That's valid against the message schema, but invalid against the reference schema.  All of the augmentation elements have to be the first children in order to match the wildcard in `structures:ObjectType`.  OK, let's reorder the message:

```
<my:Message
  <nc:Vehicle>
    <j:ItemMerchandiseIndicator>false</j:ItemMerchandiseIndicator>
    <em:ItemExpirationDate>
      <nc:YearDate>2022</nc:YearDate>
    </em:ItemExpirationDate>
    <j:ConveyanceJointlyRegisteredIndicator>true</j:ConveyanceJointlyRegisteredIndicator>
    <j:VehicleGarageIndicator>true</j:VehicleGarageIndicator>
    <nc:ItemDescriptionText>Large</nc:ItemDescriptionText>
    <nc:ItemModelYearDate>2015</nc:ItemModelYearDate>
    <nc:ConveyanceEngineQuantity>2</nc:ConveyanceEngineQuantity>
    <nc:VehicleDoorQuantity>4</nc:VehicleDoorQuantity>
    <nc:VehicleSeatingQuantity>8</nc:VehicleSeatingQuantity>
  </nc:Vehicle>
</my:Message>
```

Whoops!  That validates against the reference schema, but not against the locked-down message schema.  In the message schema the augmentation elements must appear according to the type hierarchy:  `ItemType` first, then `ConveyanceType`, finally `VehicleType`.  In order to distribute augmentation elements in that way, we need a wildcard augmentation in the reference schema for each type, like this:

```
<xs:complexType name="VehicleType">
  <xs:complexContent>
    <xs:extension base="nc:ConveyanceType">
      <xs:sequence>
        <xs:any minOccurs="0" maxOccurs="unbounded" processContents="strict"/>
        <xs:element ref="nc:VehicleDoorQuantity"/>
        <xs:element ref="nc:VehicleSeatingQuantity"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
```

Bang! Now we get UPA errors upon schema assembly!  That's not a valid schema in XSD 1.0.

So I don't see any way to handle augmentations without augmentation points.  They don't mean anything, so we don't want them in NIEM RDF, which means we can't have them in NIEM JSON.  They are just a necessary hack to get around an old design decision in XML Schema.