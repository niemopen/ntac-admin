
# RoleOf Alternate Replacement Example

[Comment with proposal from niemopen/niem-naming-design-rules Issue #6](https://github.com/niemopen/niem-naming-design-rules/issues/6#issuecomment-1424430475)

This example demonstrates the replacement of the NIEM `RoleOf` construct with type inheritance and `structures:uri` to relate separate objects in messages together as the same entity.

## Schema adjustments

In `jxdm.xsd`, adjusted `CrashDriverType` and `CrashPersonType` to:

- extend `nc:PersonType` instead of `structures:ObjectType`
- remove `nc:RoleOfPerson` as a subproperty

### Example

**Original j:CrashDriverType subset from NIEM 5.2:**

```xml
  <xs:complexType name="CrashDriverType">
    <xs:annotation>
      <xs:documentation>A data type for a motor vehicle driver involved ...</xs:documentation>
    </xs:annotation>
    <xs:complexContent>
      <xs:extension base="structures:ObjectType">
        <xs:sequence>
          <xs:element ref="nc:RoleOfPerson" minOccurs="0" maxOccurs="unbounded"/>
          <xs:element ref="j:DriverLicense" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
```

**Adjusted j:CrashDriverType subset:**

```xml
  <xs:complexType name="CrashDriverType">
    <xs:annotation>
      <xs:documentation>A data type for a motor vehicle driver involved ...</xs:documentation>
    </xs:annotation>
    <xs:complexContent>
      <xs:extension base="nc:PersonType">
        <xs:sequence>
          <xs:element ref="j:DriverLicense" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
```

## Sample instance adjustments

The two sample IEPs remove the `nc:RoleOfPerson` element from the hierarchy under `j:CrashDriver` and `j:CrashPerson`.

### Examples

*Examples are abbreviated below but can be seen in full in their corresponding XML instance files.*

**Original:**

```xml
<exch:CrashDriverInfo
  <j:Crash>
    <j:CrashVehicle>
      <j:CrashDriver>
        <nc:RoleOfPerson structures:ref="P01" xsi:nil="true"/>
        <j:DriverLicense>
          <j:DriverLicenseCardIdentification>
            <nc:IdentificationID>A1234567</nc:IdentificationID>
          </j:DriverLicenseCardIdentification>
        </j:DriverLicense>
      </j:CrashDriver>
    </j:CrashVehicle>
    <j:CrashPerson>
      <nc:RoleOfPerson structures:ref="P01" xsi:nil="true"/>
      <j:CrashPersonInjury>
        <nc:InjuryDescriptionText>Broken Arm</nc:InjuryDescriptionText>
      </j:CrashPersonInjury>
    </j:CrashPerson>
  </j:Crash>
  <j:Charge structures:id="CH01">
    <j:ChargeDescriptionText>Furious Driving</j:ChargeDescriptionText>
    <j:ChargeFelonyIndicator>false</j:ChargeFelonyIndicator>
  </j:Charge>
  <j:PersonChargeAssociation>
    <nc:Person structures:id="P01">
      <nc:PersonBirthDate>
        <nc:Date>1890-05-04</nc:Date>
      </nc:PersonBirthDate>
      <nc:PersonName nc:personNameCommentText="copied">
        <nc:PersonGivenName>Peter</nc:PersonGivenName>
        <nc:PersonMiddleName>Death</nc:PersonMiddleName>
        <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
        <nc:PersonSurName>Wimsey</nc:PersonSurName>
      </nc:PersonName>
    </nc:Person>
    <j:Charge structures:ref="CH01" xsi:nil="true"/>
  </j:PersonChargeAssociation>
</exch:CrashDriverInfo>
```

**IEP 1:**

```xml
<exch:CrashDriverInfo
  xmlns:exch="http://example.com/CrashDriver/1.1/"
  xmlns:j="http://release.niem.gov/niem/domains/jxdm/7.2/"
  xmlns:nc="http://release.niem.gov/niem/niem-core/5.0/"
  xmlns:structures="http://release.niem.gov/niem/structures/5.0/"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://example.com/CrashDriver/1.1/ CrashDriver.xsd">
  <j:Crash>
    <j:CrashVehicle>
      <j:CrashDriver structures:uri="P01">
        <j:DriverLicense>
          <j:DriverLicenseCardIdentification>
            <nc:IdentificationID>A1234567</nc:IdentificationID>
          </j:DriverLicenseCardIdentification>
        </j:DriverLicense>
      </j:CrashDriver>
    </j:CrashVehicle>
    <j:CrashPerson structures:uri="P01">
      <j:CrashPersonInjury>
        <nc:InjuryDescriptionText>Broken Arm</nc:InjuryDescriptionText>
      </j:CrashPersonInjury>
    </j:CrashPerson>
  </j:Crash>
  <j:Charge structures:id="CH01">
    <j:ChargeDescriptionText>Furious Driving</j:ChargeDescriptionText>
    <j:ChargeFelonyIndicator>false</j:ChargeFelonyIndicator>
  </j:Charge>
  <j:PersonChargeAssociation>
    <nc:Person structures:uri="P01">
      <nc:PersonBirthDate>
        <nc:Date>1890-05-04</nc:Date>
      </nc:PersonBirthDate>
      <nc:PersonName nc:personNameCommentText="copied">
        <nc:PersonGivenName>Peter</nc:PersonGivenName>
        <nc:PersonMiddleName>Death</nc:PersonMiddleName>
        <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
        <nc:PersonSurName>Wimsey</nc:PersonSurName>
      </nc:PersonName>
    </nc:Person>
    <j:Charge structures:ref="CH01" xsi:nil="true"/>
  </j:PersonChargeAssociation>
</exch:CrashDriverInfo>
```

**IEP 2:**

```xml
<exch:CrashDriverInfo
  <j:Crash>
    <j:CrashVehicle>
      <j:CrashDriver structures:uri="P01">
        <nc:PersonBirthDate>
          <nc:Date>1890-05-04</nc:Date>
        </nc:PersonBirthDate>
        <nc:PersonName nc:personNameCommentText="copied">
          <nc:PersonGivenName>Peter</nc:PersonGivenName>
          <nc:PersonMiddleName>Death</nc:PersonMiddleName>
          <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
          <nc:PersonSurName>Wimsey</nc:PersonSurName>
        </nc:PersonName>
        <j:DriverLicense>
          <j:DriverLicenseCardIdentification>
            <nc:IdentificationID>A1234567</nc:IdentificationID>
          </j:DriverLicenseCardIdentification>
        </j:DriverLicense>
      </j:CrashDriver>
    </j:CrashVehicle>
    <j:CrashPerson structures:uri="P01">
      <nc:PersonBirthDate>
        <nc:Date>1890-05-04</nc:Date>
      </nc:PersonBirthDate>
      <nc:PersonName nc:personNameCommentText="copied">
        <nc:PersonGivenName>Peter</nc:PersonGivenName>
        <nc:PersonMiddleName>Death</nc:PersonMiddleName>
        <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
        <nc:PersonSurName>Wimsey</nc:PersonSurName>
      </nc:PersonName>
      <j:CrashPersonInjury>
        <nc:InjuryDescriptionText>Broken Arm</nc:InjuryDescriptionText>
      </j:CrashPersonInjury>
    </j:CrashPerson>
  </j:Crash>
  <j:Charge structures:id="CH01">
    <j:ChargeDescriptionText>Furious Driving</j:ChargeDescriptionText>
    <j:ChargeFelonyIndicator>false</j:ChargeFelonyIndicator>
  </j:Charge>
  <j:PersonChargeAssociation>
    <nc:Person structures:uri="P01">
      <nc:PersonBirthDate>
        <nc:Date>1890-05-04</nc:Date>
      </nc:PersonBirthDate>
      <nc:PersonName nc:personNameCommentText="copied">
        <nc:PersonGivenName>Peter</nc:PersonGivenName>
        <nc:PersonMiddleName>Death</nc:PersonMiddleName>
        <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
        <nc:PersonSurName>Wimsey</nc:PersonSurName>
      </nc:PersonName>
    </nc:Person>
    <j:Charge structures:ref="CH01" xsi:nil="true"/>
  </j:PersonChargeAssociation>
</exch:CrashDriverInfo>
```

## Cardinality

The first sample IEP shows an example with person details only under `nc:Person`, directly under the root of the message.

- The cardinality of the first example cannot be enforced through NIEM XML Schema as is.
- Options:
  - Check cardinality via Schematron.
  - Generate customized message schemas with flattened types that only contain the exact sub-properties needed.
- This use case is not unique to roles.  Because of the reusable data model with global types, the same sort of issue would occur if we wanted `j:CrashDriver` to have a mandatory birthday and `j:CrashPerson` to not contain a birthday.  Or if we wanted a different subset of properties for Teacher vs Student, Subject vs EnforcementOfficial, Defendant vs JusticeOfficial, etc.

The second sample IEP shows an example with person details duplicated wherever they appear.

- This cardinality (duplicate data, avoid references) could be enforced by making `nc:PersonBirthDate` and `nc:PersonName` required under `nc:PersonType`.
