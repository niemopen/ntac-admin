# NIEM 6.0 Architecture Changes Project Note

<!-- TODO: Organization into sections -->

## Abstract

This document describes the major architectural changes planned for the NIEM Naming and Design Rules specification for version 6.0.

## New Content

- TBD

<!-- TODO: New content? -->

## Introduction of CMF

- CMF as a peer canonical format for NIEM 6.0, alongside XML Schema
- Planned to replace XML Schema as the sole canonical form in NIEM 7.0

<!-- TODO: CMF description -->

## nc:ObjectType

https://github.com/niemopen/ntac-admin/discussions/58

<!-- TODO: Determine nc:ObjectType recommendation -->

CMF either needs to be aware of some parts of the `structures` namespace, or we need a `nc:ObjectType` in the model because domains and messages should be allowed to augment the root-level `ObjectType`.

Notes:

- NIEM 1.0 had a similar `u:SuperType`.
- `nc:CodeType` contains three attributes from the code-lists-instance utility namespace
- A new rule is being proposed to prevent properties with types from the `structures` namespace
  - https://github.com/niemopen/niem-naming-design-rules/issues/29
- If CMF was aware of `structures`, then we could say that `nc:PersonType` or `j:PersonEyeColorCode` contains `structures:metadata`.
- If CMF is updated to support customized profiles of objects and was `structures`-aware, we could say:
  - `nc:Person` contains `structures:id` and `hs:Caregiver` contains `structures:ref`.
  - `nc:PersonType` contains `structures:uri` as required and not have `structures:uri` appear elsewhere.

## Removal of structures:sequenceID

[niemopen/niem-naming-design-rules#19](https://github.com/niemopen/niem-naming-design-rules/issues/19)

<!-- TODO: structures:sequenceID -->

- Replace with indicator that order matters
- new `appinfo:orderedPropertyIndicator` (or perhaps `appinfo:isOrdered`?)

## Attribute wildcards

[niemopen/niem-naming-design-rules#20](https://github.com/niemopen/niem-naming-design-rules/issues/20)

<!-- TODO: Attribute wildcards -->

## Allow facets on EXT complex value types

[niemopen/niem-naming-design-rules#9](https://github.com/niemopen/niem-naming-design-rules/issues/9)

<!-- TODO: Facets on complex value types -->

- Enumerations on simple types support attributes and type unions
- Complex types support attributes from structures and are required for element types
- Allowing enumerations on complex value types or elements with simple types should be supported options when generating message schemas

## Simplified Roles

[niemopen/niem-naming-design-rules#6](https://github.com/niemopen/niem-naming-design-rules/issues/6)

The NTAC is considering a proposal to simplify roles within NIEM:

NDR 6.0 remove the special syntax and rules related to roles.  Rather than requiring special `RoleOf` properties to be contained within role types, roles would be implemented via specialization and the use of the existing `structures:uri` attribute to link related objects to the same entity.

### Background

Roles are used in NIEM to represent a non-exclusive function or part played by an object. An object may have one or more roles.  For example, victim, witness, and officer can all be roles of a person.  One person may end up playing each of these roles within the same message.

Roles differ from specialization in NIEM, which is reserved for typically-exclusive special functions of an object.  For example, a vehicle (e.g., a car) and an aircraft are specializations of a conveyance, which is a specialization of an item.

Exclusivity is the key difference between roles and specialization.  One person might end up participating in multiple roles within the same message (e.g., victim, witness, and officer), while a vehicle is a specialization because it is typically not also a plane, train, firearm, or cellphone at the same time.

There is some special syntax used for roles that allow us to link related occurrences together in messages so that it is clear they each represent the same entity.

The following are the main techniques in NIEM for creating new classes based on existing classes.

Technique | Use case | Schema
--------- | -------- | ------
Roles | Non-exclusive function of an object. <br/><br/> Example: Victim, witness, officer, teacher, parent, child are all roles of a person object. | Extend `structures:ObjectType` and use one or more `RoleOf` properties
Augmentation | Mechanism used to "drop in" additional content into an existing type from another namespace without actually making changes to it. <br/><br/> Example: Justice, Emergency Management, Biometrics, and other NIEM domains each define their own augmentation for nc:PersonType with common person-related properties within their subject area. | Substitution groups
Specialization | Exclusive special function of an object. <br/><br/> Example: A vehicle (car) is a  specialization of conveyance, which is a specialization of item. | Type extension

### Requirements

The rules and syntax for the current role mechanism fulfills two requirements:

1. Support explicitly identifying occurrences in a message where multiple properties refer back to the same entity.
2. Support referencing to avoid the need to duplicate data in messages.  For example, a person name and birthday could be defined once in a message and referenced multiple times.

### Proposal

- Eliminate roles as a special technique.
- Use specialization for both exclusive and non-exclusive special functions of an object.
- Use the existing `structures:uri` attribute to link related roles back to the same object.

The syntax for roles was developed as part of NIEM 1.0.  The `structures:uri` attribute was not added until NIEM 4.0.  This attribute can be leveraged to meet the same requirements without the special role rules or syntax.

### Examples

These examples are based on the Crash Driver IEPD using in NIEM training.  Role types are modified to extend `nc:PersonType` and to remove the `nc:RoleOfPerson` property.

#### Schema example

Notes:

- The parent type of `CrashDriverType` changes from `structures:ObjectType` to `nc:PersonType`.
- The `nc:RoleOfProperty` is no longer needed.
- The `structures:uri` attribute is inherited by `CrashDriverType` and by any other type that extends from `structures:ObjectType` (which is the parent of `nc:PersonType`).

```diff
  <xs:complexType name="CrashDriverType">
    <xs:annotation>
      <xs:documentation>A data type for a motor vehicle driver involved ...</xs:documentation>
    </xs:annotation>
    <xs:complexContent>
-     <xs:extension base="structures:ObjectType">
+     <xs:extension base="nc:PersonType">
        <xs:sequence>
-         <xs:element ref="nc:RoleOfPerson" minOccurs="0" maxOccurs="unbounded"/>
          <xs:element ref="j:DriverLicense" minOccurs="0" maxOccurs="unbounded"/>
          <!-- ... -->
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
```

#### Message example 1, without repeating data

Notes:

- The use of `structures:id` and `structures:ref` is replaced by the use of `structures:uri`.
- The `nc:RoleOfPerson` properties are removed.

```diff
<exch:CrashDriverInfo>
+ <nc:Person structures:uri="P01">
- <nc:Person structures:id="P01">
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
  <j:Crash>
    <j:CrashVehicle>
+     <j:CrashDriver structures:uri="P01">
-     <j:CrashDriver>
-       <nc:RoleOfPerson structures:ref="P01" xsi:nil="true"/>
        <j:DriverLicense>
          <j:DriverLicenseCardIdentification>
            <nc:IdentificationID>A1234567</nc:IdentificationID>
          </j:DriverLicenseCardIdentification>
        </j:DriverLicense>
      </j:CrashDriver>
    </j:CrashVehicle>
+   <j:CrashPerson structures:uri="P01">
-   <j:CrashPerson>
-     <nc:RoleOfPerson structures:ref="P01" xsi:nil="true"/>
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
+   <nc:Person structures:uri="P01" xsi:nil="true"/>
-   <nc:Person structures:ref="P01" xsi:nil="true"/>
    <j:Charge structures:ref="CH01" xsi:nil="true"/>
  </j:PersonChargeAssociation>
</exch:CrashDriverInfo>
```

#### Message example 2, with duplicated data

Notes:

- `structures:uri` has been available since NIEM 4.0 and can already be used to link multiple properties to the same entity.
- Data is repeated in the message below, which makes the message longer but can make it easier to process.  This option is already available with the current role technique.  This proposal would simply eliminate the extra `nc:RoleOfPerson` properties from the message.

```diff
<exch:CrashDriverInfo>
  <j:Crash>
    <j:CrashVehicle>
      <j:CrashDriver structures:uri="P01">
-       <nc:RoleOfPerson>
          <nc:PersonBirthDate>
            <nc:Date>1890-05-04</nc:Date>
          </nc:PersonBirthDate>
          <nc:PersonName nc:personNameCommentText="copied">
            <nc:PersonGivenName>Peter</nc:PersonGivenName>
            <nc:PersonMiddleName>Death</nc:PersonMiddleName>
            <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
            <nc:PersonSurName>Wimsey</nc:PersonSurName>
          </nc:PersonName>
-       </nc:RoleOfPerson>
        <j:DriverLicense>
          <j:DriverLicenseCardIdentification>
            <nc:IdentificationID>A1234567</nc:IdentificationID>
          </j:DriverLicenseCardIdentification>
        </j:DriverLicense>
      </j:CrashDriver>
    </j:CrashVehicle>
    <j:CrashPerson structures:uri="P01">
-     <nc:RoleOfPerson>
        <nc:PersonBirthDate>
          <nc:Date>1890-05-04</nc:Date>
        </nc:PersonBirthDate>
        <nc:PersonName nc:personNameCommentText="copied">
          <nc:PersonGivenName>Peter</nc:PersonGivenName>
          <nc:PersonMiddleName>Death</nc:PersonMiddleName>
          <nc:PersonMiddleName>Bredon</nc:PersonMiddleName>
          <nc:PersonSurName>Wimsey</nc:PersonSurName>
        </nc:PersonName>
-     </nc:RoleOfPerson>
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

### Benefits

1. Roles are part of the learning curve for new users.  They require special instruction not only when new users are building their own schemas, but also when they are just browsing the content of NIEM.  Users can look at roles like witness or student and not know that person properties are already available.

2. Roles are also easy to forget to model correctly for more experienced users.  QA checks catch a lot of cases where specialization is used in place of roles.

3. The more-complicated special role syntax is always required, no matter whether or not messages will leverage the referencing capabilities to link related properties together.  There is no way to opt out of the syntax if it is not needed.

4. The current NIEM role technique results in inconsistencies..  `RoleOf` properties are only available when custom role types must be created.

    For example, the Human Services domain had special student-related properties, so they created `hs:StudentType` as a role of `nc:PersonType`.  Student properties will contain `nc:RoleOfPerson`.  However, there were no special properties required for caregiver, so property `hs:Caregiver` is directly of type `nc:PersonType`.

    This makes the representation of people within the model inconsistent.  You have to look at the declaration of the type to determine if an entity is-a person, or has-a role-of-person.  This can make it more difficult to process and query data:

    `hs:Student` -> `nc:RoleOfPerson` -> `nc:PersonName`
    `hs:Caregiver` -> `nc:PersonName`

    Without looking at the schema or memorizing the model, it's almost impossible to know if `j:Counselor` is defined as a person or as a role of a person.

5. The custom role rules and syntax may no longer provide enough benefit to justify the complexities now that the `structures:uri` attribute is available.  This attribute can be leveraged already to link related properties together, whether or not the role syntax remains in place.

### Drawbacks

1. Almost 100 types in NIEM 5.2 contain a RoleOf property and would need to be adjusted.  This would likely affect many migrated message schemas and their instances.

2. RoleOf properties allow the validation of additional cardinality constraints through XML Schema.  Because `structure:id` and `structures:ref` can be used with `RoleOf` properties, properties on the object of a role type can be marked as required, but do not have to appear when `structures:ref` is used.

    From the first instance example, `nc:PersonName` and `nc:PersonBirthDate` could be set as required (`minOccurs="1"`) under `nc:PersonType`, but wouldn't have to appear under `j:CrashDriver` or `j:CrashPerson` because these two properties use `structures:ref` to point to where their data is contained.

    Removing these special `RoleOf` properties would mean that `nc:PersonName` and `nc:PersonBirthDate` would need to be set as optional (`minOccurs="0"`) if they are supposed to appear under `nc:Person` but not appear under `j:CrashDriver` or `j:CrashPerson`.  This would be similar to other business rules that cannot be implemented via XML Schema and rely on validation through Schematron or other application validation.

    It may also be possible in the future to generated customized message schemas that hardcode exactly which properties can appear by replacing the object-oriented nature of the reusable NIEM reference data model with flattened types for improved validation.

## Metadata Changes

[niem-open/niem-naming-design-rules#8](https://github.com/niemopen/niem-naming-design-rules/issues/8)
[niem-open/niem-naming-design-rules#21](https://github.com/niemopen/niem-naming-design-rules/issues/21)

<!-- TODO: Metadata -->

- No metadata types or attributes
- Attribute wildcards, but not element wildcards
- Augmentation replacing metadata attributes
- Use augmentation to add a metadata object to complex content
- Use a reference attribute to add a metadata object to simple content
- Applying metadata to simple content will require the user to create the appropriate object
	- Simple content no longer has attributes unless you put them there on purpose
	- which is discouraged, because it makes NIEM JSON more complicated.

### Details from 2022-08-15 Face-to-Face

<!-- TODO: Face-to-Face details -->

- Metadata objects can now be included directly in the objects to which they apply
	- Referencing no longer _required_
	- Referencing still _allowed_ if needed
- Clarified scope rules, two alternatives here:
	1. Better definitions about which metadata objects apply where
		- Metadata applies to enclosing object, unless...
		- If the metadata has an `id` or `uri` it doesn't apply to the enclosing object
			- So it can be a target
	2. `@structures:onlyRef="true"` on elements that do not apply to their parent
- Allow augmentations in metadata
	- Treat metadata objects more like normal objects
- Drop several things from `SimpleObjectAttributeGroup`
	- `relationshipMetadata` (but otherwise keep)
	- `id`
	- `ref`
	- `uri`
- Drop several things from `AugmentationType`
	- `id`
	- `ref`
	- `uri`
	- `ism`
- Add NDR _instance_ rule saying you can only have one copy of an augmentation object per namespace
	- Can have just one `j:PersonAugmentation`, but could also have a `j:CitationAugmentation` and/or an `hs:PersonAugmentation`
- Make changes per [2022-07-12 metadata-adjustments-2](2022-07-12-metadata-adjustments-2.md)

## Add representation term "Adapter"

[niemopen/niem-naming-design-rules#5](https://github.com/niemopen/niem-naming-design-rules/issues/5)

NIEM already has representation terms for associations, augmentations, and metadata.  Adding representation term "Adapter" for properties and types would make it clear which components contain non-conformant content from external standards.  A few properties and types currently in NIEM already follow this convention.

**Impact**

The following is a list of adapter properties and types from NIEM 5.2.  These names, unless otherwise noted, would be updated.  Property names would end in "Adapter"; type names would end in "AdapterType".

| Property                           | Type                                   | Notes
|:-----------------------------------|:---------------------------------------|:-----
| ag:LocationLineStringCoordinates   | geo:LineStringType                     |
| ag:LocationMultiSurfaceCoordinates | geo:MultiSurfaceType                   |
| ag:LocationPointCoordinates        | geo:PointType                          |
| ag:LocationPolygonCoordinates      | geo:PolygonType                        |
| cbrn:SpecialEventSecurityArea      | geo:PolygonType                        |
| edxl-cap:AlertAdapter              | edxl-cap:AlertAdapterType              | No change
| edxl-de:DistributionElementAdapter | edxl-de:DistributionElementAdapterType | No change
| edxl-have:HaveAdapter              | edxl-have:HaveAdapterType              | No change
| geo:AreaCurve                      | geo:CurveType                          |
| geo:AreaEnvelope                   | geo:EnvelopeType                       |
| geo:AreaPoint                      | geo:PointType                          |
| geo:AreaPolygon                    | geo:PolygonType                        |
| geo:AreaRegionGeometry             | geo:GeometryType                       |
| geo:Ellipse                        | geo:EllipseType                        |
| geo:Feature                        | geo:FeatureType                        |
| geo:Geometry                       | geo:GeometryType                       |
| geo:LocationFeature                | geo:FeatureType                        |
| geo:LocationGeometry               | geo:GeometryType                       |
| geo:LocationGeospatialPoint        | geo:PointType                          |
| mo:WaypointPoint                   | geo:PointType                          |
| mo:WGS84LocationEllipse            | mo:WGS84EllipseType                    |
| mo:WGS84LocationExternalPolygon    | mo:WGS84ExternalPolygonType            |
| mo:WGS84LocationLineString         | mo:WGS84LineStringType                 |
| mo:WGS84LocationPoint              | mo:WGS84LocationPointType              |

## Create new type `structures:AdapterType`

Types in the structures namespace are generally used to identify what kind of type something is (an object type, an association type, a metadata type, etc.).  Adapter types do not follow the same pattern.  They require the use of a special attribute to identify themselves as adapters.

| Type               | Rule                                              |
|:-------------------|:--------------------------------------------------|
| association types  | Extend structures:AssociationType or a derivative |
| augmentation types | Extend structures:AugmentationType                |
| metadata types     | Extend structures:MetadataType                    |
| object types       | Extend structures:ObjectType or a derivative      |
| adapter types      | Extend **structures:ObjectType** and use **appinfo:externalAdapterTypeIndicator="true"** |

**Proposal**

- Add a new `structures:AdapterType` to serve as the parent type for all adapter types defined in NIEM.
- Remove attribute `appinfo:externalAdapterTypeIndicator` from the appinfo utility namespace.

**Example declaration of an adapter type**

```diff
- <xs:complexType name="GeometryType" appinfo:externalAdapterTypeIndicator="true">
+ <xs:complexType name="GeometryType">
   <xs:complexContent>
-    <xs:extension base="structures:ObjectType">
+    <xs:extension base="structures:AdapterType">
       <xs:sequence>
         <xs:element ref="gml:AbstractGeometry" minOccurs="1" maxOccurs="1"/>
       </xs:sequence>
     </xs:extension>
   </xs:complexContent>
 </xs:complexType>
```

## Disallow Direct Use of Structures Typing

[niemopen/niem-naming-design-rules#29](https://github.com/niemopen/niem-naming-design-rules/issues/29)

Disallow making elements directly from types defined in `structures`.

**Impact**

https://github.com/niemopen/niem-model/issues/24

`cbrn:CaseMetadata` and `cbrn:DataFileMetadata` are currently of type `structures:MetadataType`.  These two properties would need to be updated, perhaps to be of type `nc:MetadataType`.

## Unique Enumerations

[niemopen/niem-naming-design-rules#30](https://github.com/niemopen/niem-naming-design-rules/issues/30)

There are some code types in NIEM that repeat the same enumeration with different definitions.  A new rule is being proposed to require that enumerations are unique within a type.  For code sources outside of NIEM where duplication cannot be resolved, definitions of overlapping codes will be concatenated.

**NIEM 5.2 Example**

| Type                            | FacetValue | Definition |
|---------------------------------|------------|------------|
| usps:StreetSuffixCodeSimpleType | OVAL       | OVAL       |
| usps:StreetSuffixCodeSimpleType | PARK       | PARK       |
| usps:StreetSuffixCodeSimpleType | PARK       | PARKS      |
| usps:StreetSuffixCodeSimpleType | PASS       | PASS       |

**NIEM 6.0 Proposal**

| Type                            | FacetValue | Definition |
|---------------------------------|------------|------------|
| usps:StreetSuffixCodeSimpleType | OVAL       | OVAL       |
| usps:StreetSuffixCodeSimpleType | PARK       | PARK or PARKS |
| usps:StreetSuffixCodeSimpleType | PASS       | PASS       |

**Impact**

There are 16 types in NIEM 5.2 that do not have unique enumerations:

- can:StreetDirectionCodeSimpleType
- cbrncl:FacilityUsageCodeSimpleType
- cyber:BreachClassificationCategoryCodeSimpleType
- dea:DrugCodeSimpleType
- em:BarcodeCodeSimpleType
- em:NotificationFunctionCategoryCodeSimpleType
- fips:USCounty3DigitCodeSimpleType
- hazmat:HazmatCodeSimpleType
- mmucc:DriverLicenseClassCodeSimpleType
- mo:FrequencyUnitTemporalCodeSimpleType
- mo:RegisteredServiceNameCodeSimpleType
- usmtf:AngleUnitCodeSimpleType
- usmtf:NauticalMileUnitCodeSimpleType
- usmtf:RadioactiveHalfLifeCodeSimpleType
- usmtf:RFPowerUnitDecibelsCodeSimpleType
- usps:StreetSuffixCodeSimpleType

See comments on the GitHub issue (link above) for more details on the affected types and codes.

**Special considerations**

Canada Post street direction codes provide two definitions per enumerations: one definition in English and one definition in French.

## Require definitions for patterns

[niemopen/niem-naming-design-rules#12](https://github.com/niemopen/niem-naming-design-rules/issues/12)

NIEM currently requires definitions for enumeration facets only.  Definitions for pattern facets should provide extra human-readable documentation to make the regular expressions easier to interpret.

### Impact

There are 438 patterns in NIEM 5.2:

- 433 patterns have definitions
- 5 patterns do not have definitions

Type | Pattern
---- | -------
ag_codes:TaxIdentificationIDSimpleType | `[0-9]{9}`
ag_codes:CropYearSimpleType | `^([1][9]\d\d\|[2]\d\d\d)$`
biom:LipPatternSimpleType | `<>`
mo:MILSTD2525-C-SIDC-SimpleType | `[A-Z0-9\-]{15}`
mo:MILSTD2525-B-SIDC-SimpleType | `[A-Z0-9\-]{15}`

## XSD Conformance Targets

<!-- TODO: Conformance targets -->

- ReferenceSchema - has open content (attribute wildcards in structures, for augmentations)
- SubsetSchema - now marked with EXT conformance target to minimize conformance issues
- ExtensionSchema - like ReferenceSchema but `xs:any` is OK
- MessageSchema - for validation & binding, not for model semantics
	- Closed content (no attribute wildcards)
	- `xs:choice` OK
	- local type definitions OK
	- local element and attribute declarations OK

## Utility Namespace Changes

<!-- TODO: Utility changes -->

- `appinfo.xsd`
	- Additions:
		- new `appinfo:orderedPropertyIndicator` (or perhaps `appinfo:isOrdered`?)
		- new `appinfo:relationshipPropertyIndicator` (or perhaps `appinfo:isRelationshipProperty`?)
		- new `appinfo:referenceableIndicator` (or perhaps `isReferenceable`?)
		- new `appinfo:referenceAttributeIndicator` (or perhaps `isReference`?)
	- Subtractions:
		- no `appinfo:appliesToElements`
		- no `appinfo:appliesToTypes`

- `structures.xsd`
	- Additions:
		- new attribute `@structures:onlyRef` (True for an XML element that does not apply to its parent.)
		- new attribute `@structures:qname` (like URI)
	- `ObjectType` contains an attribute wildcard (only in reference schemas)
	- `AssociationType` contains an attribute wildcard (only in reference schemas)
 - Subtractions
	- no `MetadataType`
	- no attributes on `AugmentationType`
	- no `@metadata` attribute
	- no `@relationshipMetadata` attribute
	- no `@sequenceID` attribute
	- no `SimpleObjectAttributeGroup`
	- no attribute wildcard for ISM, NTK namespaces

<!-- TODO: Utility namespace changes -->

## Other

<!-- TODO: Other -->

- Relative URIs are blank nodes in messages with no base URI.

- `@xml:lang` only applies to elements with a string-valued CSC that includes `@xml:lang`
	- Doesn't apply to attribute values at all.
