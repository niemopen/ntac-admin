
![OASIS Logo](https://docs.oasis-open.org/templates/OASISLogo-v3.0.png)

## OASIS Project Note
-------

# NIEM 6.0 Architectural Changes Version 1.0

## Project Note 01

## 05 June 2023

&nbsp;

#### This stage:
https://docs.oasis-open.org/niem/niem-6.0-arch-changes/v1.0/pn01/niem-6.0-arch-changes-v1.0-pn01.md (Authoritative) \
https://docs.oasis-open.org/niem/niem-6.0-arch-changes/v1.0/pn01/niem-6.0-arch-changes-v1.0-pn01.html \
https://docs.oasis-open.org/niem/niem-6.0-arch-changes/v1.0/pn01/niem-6.0-arch-changes-v1.0-pn01.pdf

#### Previous stage:
N/A

#### Latest stage:
https://docs.oasis-open.org/niem/niem-6.0-arch-changes/v1.0/niem-6.0-arch-changes-v1.0.md (Authoritative) \
https://docs.oasis-open.org/niem/niem-6.0-arch-changes/v1.0/niem-6.0-arch-changes-v1.0.html \
https://docs.oasis-open.org/niem/niem-6.0-arch-changes/v1.0/niem-6.0-arch-changes-v1.0.pdf

#### Open Project:
[NIEM Technical Architecture Committee (NTAC) of the OASIS NIEMOpen OP](http://www.niemopen.org/)

#### Project Chair:
Katherine Escobar (katherine.b.escobar.civ@mail.mil), [Joint Staff J6](https://www.jcs.mil/Directorates/J6-C4-Cyber/)

#### NTAC Committee Chairs:
Jim Cabral (jim.cabral@infotrack.com), [InfoTrack](https://www.infotrack.com/) \
Scott Renner (sar@mitre.org), [MITRE](https://www.mitre.org/)

#### Editors:
Thomas Carlson (thomas.carlson@gtri.gatech.edu), [GTRI](https://gtri.gatech.edu/) \
Christina Medlin (christina.medlin@gtri.gatech.edu), [GTRI](https://gtri.gatech.edu/) \
Scott Renner (sar@mitre.org), [MITRE](https://www.mitre.org/)

#### Related work:
This document is related to:
* Related specifications (include hyperlink, preferably to HTML format) \
`(remove "Related work" section if no entries)`

#### Abstract:
This document describes the major architectural changes planned for the NIEM Naming and Design Rules specification for version 6.0.

#### Status:
This is a Non-Standards Track Work Product. The patent provisions of the OASIS IPR Policy do not apply.

This document was last revised or approved by the Project Governing Board of the OASIS NIEMOpen OP on the above date. The level of approval is also listed above. Check the "Latest stage" location noted above for possible later revisions of this document. Any other numbered Versions and other technical work produced by the Open Project (OP) are listed at http://www.niemopen.org/.

Comments on this work can be provided by opening issues in the project repository or by sending email to the project's public comment list: niemopen-comment@lists.oasis-open-projects.org. List information is available at https://lists.oasis-open-projects.org/g/niemopen-comment.

#### Citation format:
When referencing this document the following citation format should be used:

**[NIEM-6-Arch-Changes-v1.0]**

_NIEM 6.0 Architectural Changes Version 1.0_. Edited by Thomas Carlson, Christina Medlin, and Scott Renner. 05 June 2023. OASIS Project Note 01. https://docs.oasis-open.org/niem/niem-6.0-arch-changes/v1.0/pn01/niem-6.0-arch-changes-v1.0-pn01.html. Latest stage: https://docs.oasis-open.org/niem/niem-6.0-arch-changes/v1.0/niem-6.0-arch-changes-v1.0.html.

#### Notices
Copyright &copy; OASIS Open 2023. All Rights Reserved.

Distributed under the terms of the OASIS [IPR Policy](https://www.oasis-open.org/policies-guidelines/ipr/).

For complete copyright information please see the full Notices section in an Appendix below.

-------

# Table of Contents
[[TOC will be inserted here]]

-------

<!-- Insert a "line rule" (three or more hyphens alone on a new line, following a blank line) before each major section. This is used to generate a page break in the PDF format. -->

# 1 Introduction

This document describes the major architectural changes planned for the NIEM Naming and Design Rules specification for version 6.0.

## 1.2 Glossary

<!-- Optional section with suggested subsections -->

### 1.2.1 Definitions of terms

| Term | Description |
|:---- |:----------- |
| Class | A type that contains properties
| Object property | A property that has a class type
| Datatype | A type that contains attribute properties and carries a value (e.g., a string, number, boolean, etc.)
| Data property | A property that has a datatype.

### 1.2.2 Acronyms and abbreviations

| Term | Description |
|:---- |:----------- |
| CMF | Common Model Format |
| EXT | NDR conformance target for Extension Schema Document (less strict rule set for message extension schemas) |
| IC-ISM | Intelligence Community Information Security Markings |
| IC-NTK | Intelligence Community Need-to-Know |
| NDR | Naming and Design Rules |
| REF | NDR conformance target for Reference Schema Document (stricter rule set for reusable reference schemas) |

-------

# 2 General changes

## 2.1 Common Model Format (CMF)

NIEM is in the process of introducing a platform- and technology-independent representation for NIEM data models via the new Common Model Format (CMF).  This is a format that will represent NIEM concepts and data generically so that they can then be transformed into specific languages, such as XML Schema and JSON Schema.  Transformations will typically be done via automated tooling.

The NDR will be reorganized so concepts and rules that apply to all languages are expressed in terms of CMF.  Language-specific features will be moved to supplemental representations of the NDR, such as the "NIEM Naming and Design Rules: XML Representation" (NDR-XML).  These changes will make it easier for developers to focus on only the parts of the NDR that apply to their needs.  They also enable NIEM to better support multiple representations without having to first go through the NIEM XML representation for tool support.

In addition to being used by the NDR for NIEM 6.0 to describe concepts and rules, CMF will also be used alongside XML Schema as the official canonical representations of the NIEM data model.

More information about CMF is available at https://github.com/niemopen/common-model-format.

-------

# Property changes


-------

# Type changes

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

## Allow facets on EXT complex value types

[niemopen/niem-naming-design-rules#9](https://github.com/niemopen/niem-naming-design-rules/issues/9)

Support the declaration of facets on EXT complex value types to prevent the need for message designers to define two corresponding types (one complex, one simple) for each code set.

### Background

NIEM requires that enumerations must be defined on simple types in REF schemas.  As all NIEM property elements are required to have complex types, this means that code sets and types with other kinds of facets are typically defined in pairs - a simple code type with enumerations, and a corresponding complex code type that extends the simple code type and adds the NIEM-required `structures:SimpleObjectAttributeGroup`.

#### Example: XML Schema simple and complex code types

The example below shows how code sets are typically defined in NIEM.

Notes:

- The simple code type declares the enumerations.
- The complex code type extends the simple code type and adds attributes to supports ids, referencing, linked data, metadata, and security markup via `structures:SimpleObjectAttributeGroup`.

```xml
<xs:simpleType name="AddressCategoryCodeSimpleType">
  <xs:annotation>
    <xs:documentation>A data type for a kind of address.</xs:documentation>
  </xs:annotation>
  <xs:restriction base="xs:token">
    <xs:enumeration value="business">
      <xs:annotation>
        <xs:documentation>business</xs:documentation>
      </xs:annotation>
    </xs:enumeration>
    <xs:enumeration value="registered office">
      <xs:annotation>
        <xs:documentation>registered office</xs:documentation>
      </xs:annotation>
    </xs:enumeration>
    <xs:enumeration value="residential">
      <xs:annotation>
        <xs:documentation>residential</xs:documentation>
      </xs:annotation>
    </xs:enumeration>
    <xs:enumeration value="residential or business">
      <xs:annotation>
        <xs:documentation>residential or business</xs:documentation>
      </xs:annotation>
    </xs:enumeration>
    <xs:enumeration value="unspecified">
      <xs:annotation>
        <xs:documentation>unspecified</xs:documentation>
      </xs:annotation>
    </xs:enumeration>
  </xs:restriction>
</xs:simpleType>

<xs:complexType name="AddressCategoryCodeType">
  <xs:annotation>
    <xs:documentation>A data type for a kind of address.</xs:documentation>
  </xs:annotation>
  <xs:simpleContent>
    <xs:extension base="nc:AddressCategoryCodeSimpleType">
      <xs:attributeGroup ref="structures:SimpleObjectAttributeGroup"/>
    </xs:extension>
  </xs:simpleContent>
</xs:complexType>
```

#### Benefits

These requirements provide three benefits:

1. All conformant elements in NIEM provide the ability to support ids and referencing, linked data, metadata, and security markup.
2. Facets on simple types allow those types to be used as attribute types (XML Schema does not permit attributes with complex types).
3. XML Schema supports unions of simple types, which allows a new simple type to be created as a composite of its member simple types.  This allows NIEM domains and message designers to create unions from existing code sets, or to "extend" a NIEM code set by creating a union from a NIEM simple type and an extension simple type that only contains additions to the code set (eliminates the need to duplicate existing codes in order to add additional values).

#### Drawbacks

1. The major drawback to this approach is the additional complexity it requires.  It can be cumbersome to create and maintain two types for each code set instead of a single type.

### Proposal

For message designers who do not need to create attributes or unions off of their code sets, the NTAC proposes to simplify requirements and support the declaration of facets directly on complex value types in message schemas with an EXT conformance target.

This still supports reusability in reference schemas (REF), while making message extension schemas (EXT) easier to build and maintain.

#### Example: XML Schema complex code type

```xml
  <xs:complexType name='AddressCategoryCodeType'>
    <xs:annotation>
      <xs:documentation>A data type for a kind of address.</xs:documentation>
    </xs:annotation>
    <xs:simpleContent>
      <xs:restriction base='niem-xs:token'>
        <xs:enumeration value="business">
          <xs:annotation>
            <xs:documentation>business</xs:documentation>
          </xs:annotation>
        </xs:enumeration>
        <xs:enumeration value="registered office">
          <xs:annotation>
            <xs:documentation>registered office</xs:documentation>
          </xs:annotation>
        </xs:enumeration>
        <xs:enumeration value="residential">
          <xs:annotation>
            <xs:documentation>residential</xs:documentation>
          </xs:annotation>
        </xs:enumeration>
        <xs:enumeration value="residential or business">
          <xs:annotation>
            <xs:documentation>residential or business</xs:documentation>
          </xs:annotation>
        </xs:enumeration>
        <xs:enumeration value="unspecified">
          <xs:annotation>
            <xs:documentation>unspecified</xs:documentation>
          </xs:annotation>
        </xs:enumeration>
      </xs:restriction>
    </xs:simpleContent>
  </xs:complexType>
```

#### See more

See the [example schema](./examples/complex-code-types/example.xsd) under the [complex-code-types](./examples/complex-code-types/) directory for more.

## Require unique enumerations

[niemopen/niem-naming-design-rules#30](https://github.com/niemopen/niem-naming-design-rules/issues/30)

There are some code types in NIEM that repeat the same enumeration with different definitions.  A new rule is being proposed to require that enumerations are unique within a type.  For code sources outside of NIEM where duplication cannot be resolved, definitions of overlapping codes will be concatenated.

### NIEM 5.2 Example

| Type                            | FacetValue | Definition |
|---------------------------------|------------|------------|
| usps:StreetSuffixCodeSimpleType | OVAL       | OVAL       |
| usps:StreetSuffixCodeSimpleType | PARK       | PARK       |
| usps:StreetSuffixCodeSimpleType | PARK       | PARKS      |
| usps:StreetSuffixCodeSimpleType | PASS       | PASS       |

### NIEM 6.0 Proposal

| Type                            | FacetValue | Definition |
|---------------------------------|------------|------------|
| usps:StreetSuffixCodeSimpleType | OVAL       | OVAL       |
| usps:StreetSuffixCodeSimpleType | PARK       | PARK or PARKS |
| usps:StreetSuffixCodeSimpleType | PASS       | PASS       |

### Impact

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

### Special considerations

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

-------

# Adapter changes

## Add representation term "Adapter"

[niemopen/niem-naming-design-rules#5](https://github.com/niemopen/niem-naming-design-rules/issues/5)

NIEM already has representation terms for associations, augmentations, and metadata.  Adding representation term "Adapter" for properties and types would make it clear which components contain non-conformant content from external standards.  A few properties and types currently in NIEM already follow this convention.

### Impact

The following is a list of adapter properties and types from NIEM 5.2.  These names, unless otherwise noted, would be updated.  Property names would end in "Adapter"; type names would end in "AdapterType".

| Property                           | Type   | Notes |
|:-----------------------------------|:------ |:----- |
| ag:LocationLineStringCoordinates   | geo:LineStringType |
| ag:LocationMultiSurfaceCoordinates | geo:MultiSurfaceType |
| ag:LocationPointCoordinates        | geo:PointType |
| ag:LocationPolygonCoordinates      | geo:PolygonType |
| cbrn:SpecialEventSecurityArea      | geo:PolygonType |
| edxl-cap:AlertAdapter              | edxl-cap:AlertAdapterType | No change
| edxl-de:DistributionElementAdapter | edxl-de:DistributionElementAdapterType | No change
| edxl-have:HaveAdapter              | edxl-have:HaveAdapterType | No change
| geo:AreaCurve                      | geo:CurveType |
| geo:AreaEnvelope                   | geo:EnvelopeType |
| geo:AreaPoint                      | geo:PointType |
| geo:AreaPolygon                    | geo:PolygonType |
| geo:AreaRegionGeometry             | geo:GeometryType |
| geo:Ellipse                        | geo:EllipseType |
| geo:Feature                        | geo:FeatureType |
| geo:Geometry                       | geo:GeometryType |
| geo:LocationFeature                | geo:FeatureType |
| geo:LocationGeometry               | geo:GeometryType |
| geo:LocationGeospatialPoint        | geo:PointType |
| mo:WaypointPoint                   | geo:PointType |
| mo:WGS84LocationEllipse            | mo:WGS84EllipseType |
| mo:WGS84LocationExternalPolygon    | mo:WGS84ExternalPolygonType |
| mo:WGS84LocationLineString         | mo:WGS84LineStringType |
| mo:WGS84LocationPoint              | mo:WGS84LocationPointType |

## Create new type `structures:AdapterType`

[niemopen/niem-naming-design-rules#4](https://github.com/niemopen/niem-naming-design-rules/issues/4)

Types in the structures namespace are generally used to identify what kind of type something is (an object type, an association type, a metadata type, etc.).  Adapter types do not follow the same pattern.  They require the use of a special attribute to identify themselves as adapters.

### Background

The basic category of NIEM classes can currently be identified as described in the table below:

| Type               | Rule                                              |
|:-------------------|:--------------------------------------------------|
| association types  | Extend structures:AssociationType or a derivative |
| augmentation types | Extend structures:AugmentationType                |
| metadata types     | Extend structures:MetadataType                    |
| object types       | Extend structures:ObjectType or a derivative      |
| adapter types      | Extend **structures:ObjectType** and use **appinfo:externalAdapterTypeIndicator="true"** |

### Proposal

- Add a new `structures:AdapterType` to serve as the parent type for all adapter types defined in NIEM.
- Remove attribute `appinfo:externalAdapterTypeIndicator` from the appinfo utility namespace.

#### Example declaration of an adapter type

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

-------

# Metadata Changes

## Simplify metadata

[niem-open/niem-naming-design-rules#8](https://github.com/niemopen/niem-naming-design-rules/issues/8)

NIEM currently requires the use of ids and special metadata reference attributes to link metadata and the objects to which they apply.  While this will continue to be necessary to link metadata consisting of elements to NIEM data properties (elements that carry attributes and a value), it is not necessary for object properties, which can contain the metadata directly or via augmentation.

### Background

**Current example**

```xml
<ext:Message>
  <nc:Person structures:metadata="m1 m2">
    <nc:PersonBirthDate>
      <nc:Date>1945-12-01</nc:Date>
    </nc:PersonBirthDate>
    <nc:PersonName>
      <nc:PersonFullName>John Doe</nc:PersonFullName>
    </nc:PersonName>
  </nc:Person>

  <nc:Metadata structures:id="m1">
    <nc:SourceText>Adam Barber</nc:SourceText>
    <nc:ReportedDate>
      <nc:Date>2005-04-26</nc:Date>
    </nc:ReportedDate>
  </nc:Metadata>

  <j:Metadata structures:id="m2">
    <j:CriminalInformationIndicator>true</j:CriminalInformationIndicator>
  </j:Metadata>
</ext:Message>
```

Notes:

1. NIEM currently requires metadata types to only extend `structures:MetadataType`.  It is not possible to extend `j:MetadataType` from `nc:MetadataType` so that metadata that is related but defined in separate namespaces can appear in the same metadata object.
2. To apply the two metadata objects to the `nc:Person` property in the message, `nc:Person` is required to use the `structures:metadata` attribute (or the `structures:relationshipMetadata` attribute) and reference the ids of any applicable metadata objects in the message.

### Proposal

The NTAC proposes the following changes to simplify the use of metadata in messages:

1. Allow metadata within a type or augmentation to apply
2. Allow metadata types to extend and augment other metadata types
3. Remove the `appinfo:appliesToProperties` and `appinfo:appliesToTypes` attributes on metadata, as these are rarely use and applicability can be provided by adding metadata to the types.

Under the new proposal, metadata can be added directly to types just like other kinds of properties.  To add metadata to a type in another namespace, extension or augmentation may be used.

#### Inline metadata

The following example shows metadata attached to a person via augmentation:

##### Updated inline example

```xml
<ext:Message>
  <nc:Person structures:metadata="m1 m2">
    <nc:PersonBirthDate>
      <nc:Date>1945-12-01</nc:Date>
    </nc:PersonBirthDate>
    <nc:PersonName>
      <nc:PersonFullName>John Doe</nc:PersonFullName>
    </nc:PersonName>
    <ext:PersonAugmentation>
      <j:Metadata>
        <nc:SourceText>Adam Barber</nc:SourceText>
        <nc:ReportedDate>
          <nc:Date>2005-04-26</nc:Date>
        </nc:ReportedDate>
        <j:CriminalInformationIndicator>true</j:CriminalInformationIndicator>
      </j:Metadata>
    </ext:PersonAugmentation>
  </nc:Person>
</ext:Message>
```

Notes:

1. In the example, the Justice domain's `MetadataType` extends the `MetadataType` from Core, so there is no longer a need to maintain two separate metadata objects in the instance.
2. The message designer created an augmentation for `nc:PersonType` to add the Justice Metadata property.  This metadata now applies to the person object in which it is contained without the need for special ids or references.

#### Reference metadata

The proposal updates metadata in NIEM so that they are treated much more similarly to regular objects.  Referencing via `structures:id` and `structures:ref` is supported for objects and would now also be supported for metadata.

##### Updated reference example

The following example adds a second person to the message.  In order to have the same metadata apply to both people without duplicating information, a reference to the metadata object can be added to each person:

```xml
<ext:Message>
  <nc:Person>
    <nc:PersonBirthDate>
      <nc:Date>1945-12-01</nc:Date>
    </nc:PersonBirthDate>
    <nc:PersonName>
      <nc:PersonFullName>John Doe</nc:PersonFullName>
    </nc:PersonName>
    <ext:PersonAugmentation>
      <j:Metadata structures:ref="m3">
    </ext:PersonAugmentation>
  </nc:Person>
  <nc:Person>
    <nc:PersonName>
      <nc:PersonFullName>Alice Smith</nc:PersonFullName>
    </nc:PersonName>
    <ext:PersonAugmentation>
      <j:Metadata structures:ref="m3">
    </ext:PersonAugmentation>
  </nc:Person>
  <j:Metadata structures:id="m3">
    <nc:SourceText>Adam Barber</nc:SourceText>
    <nc:ReportedDate>
      <nc:Date>2005-04-26</nc:Date>
    </nc:ReportedDate>
    <j:CriminalInformationIndicator>true</j:CriminalInformationIndicator>
  </j:Metadata>
</ext:Message>
```

#### Metadata for data properties

While metadata objects can be added to class types (types that contain properties), the same is not true for datatypes (types that contain a value and attributes).  While the [Attribute Wildcards](#attribute-wildcards) proposal in this document enables adding new attributes to existing object and data properties, attaching metadata consisting of object properties (elements) still requires special handling.  NIEM datatypes will continue to carry the `structures:metadata` and `structures:relationshipMetadata` attributes to that data properties can link to applicable metadata.

##### Example

The example below shows how to use references to apply metadata to a mix of object and data properties.

```xml
<ext:Message>
  <nc:Person>
    <nc:PersonBirthDate>
      <nc:Date>1945-12-01</nc:Date>
    </nc:PersonBirthDate>
    <nc:PersonName>
      <nc:PersonFullName>John Doe</nc:PersonFullName>
    </nc:PersonName>
    <ext:PersonAugmentation>
      <j:Metadata structures:ref="m3">
    </ext:PersonAugmentation>
  </nc:Person>
  <nc:Person>
    <nc:PersonName>
      <nc:PersonFullName>Alice Smith</nc:PersonFullName>
    </nc:PersonName>
    <ext:PersonAugmentation>
      <j:Metadata structures:ref="m3">
    </ext:PersonAugmentation>
  </nc:Person>
  <j:Metadata structures:id="m3">
    <nc:SourceText>Adam Barber</nc:SourceText>
    <nc:ReportedDate>
      <nc:Date>2005-04-26</nc:Date>
    </nc:ReportedDate>
    <j:CriminalInformationIndicator>true</j:CriminalInformationIndicator>
  </j:Metadata>
</ext:Message>
```

#### Benefits

- Drops the learning curve for new users.  Message designers can treat metadata like regular objects.
- Simple use cases require no special syntax or constructs.  Metadata can be treated like regular data.
- Advanced use cases are still possible.  References can be used to avoid duplication of data and to link metadata objects to data properties.

#### Drawbacks

- Introduces changes to the way metadata is defined and processed.
- Requires special handling to specify that the metadata does not apply to its container in the case where references are being used.

## Represent relationshipMetadata via RDF-star and JSON-LD-star

[niem-open/niem-naming-design-rules#21](https://github.com/niemopen/niem-naming-design-rules/issues/21)

<!-- TODO: Relationship Metadata -->

- No metadata types or attributes
- Augmentation replacing metadata attributes
- Applying metadata to simple content will require the user to create the appropriate object
	- Simple content no longer has attributes unless you put them there on purpose
	- which is discouraged, because it makes NIEM JSON more complicated.

-------

# Role changes

## Simplified Roles

[niemopen/niem-naming-design-rules#6](https://github.com/niemopen/niem-naming-design-rules/issues/6)

The NTAC is considering a proposal to simplify roles within NIEM.  The proposal will remove the special syntax and rules related to roles.  Rather than requiring special `RoleOf` properties to be contained within role types, roles would be implemented via specialization and the use of the existing `structures:uri` attribute to link related objects to the same entity.

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

#### Requirements

The rules and syntax for the current role mechanism fulfills two requirements:

1. Support explicitly identifying occurrences in a message where multiple properties refer back to the same entity.
2. Support referencing to avoid the need to duplicate data in messages.  For example, a person name and birthday could be defined once in a message and referenced multiple times.

### Proposal

- Eliminate roles as a special technique.
- Use specialization for both exclusive and non-exclusive special functions of an object.
- Use the existing `structures:uri` attribute to link related roles back to the same object.

The syntax for roles was developed as part of NIEM 1.0.  The `structures:uri` attribute was not added until NIEM 4.0.  This attribute can be leveraged to meet the same requirements without the special role rules or syntax.

#### Examples

These examples are based on the Crash Driver IEPD using in NIEM training.  Role types are modified to extend `nc:PersonType` and to remove the `nc:RoleOfPerson` property.

##### Schema example

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

##### Message example 1, without repeating data

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

##### Message example 2, with duplicated data

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

#### Benefits

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

#### Drawbacks

1. Almost 100 types in NIEM 5.2 contain a RoleOf property and would need to be adjusted.  This would likely affect many migrated message schemas and their instances.

2. RoleOf properties allow the validation of additional cardinality constraints through XML Schema.  Because `structure:id` and `structures:ref` can be used with `RoleOf` properties, properties on the object of a role type can be marked as required, but do not have to appear when `structures:ref` is used.

    From the first instance example, `nc:PersonName` and `nc:PersonBirthDate` could be set as required (`minOccurs="1"`) under `nc:PersonType`, but wouldn't have to appear under `j:CrashDriver` or `j:CrashPerson` because these two properties use `structures:ref` to point to where their data is contained.

    Removing these special `RoleOf` properties would mean that `nc:PersonName` and `nc:PersonBirthDate` would need to be set as optional (`minOccurs="0"`) if they are supposed to appear under `nc:Person` but not appear under `j:CrashDriver` or `j:CrashPerson`.  This would be similar to other business rules that cannot be implemented via XML Schema and rely on validation through Schematron or other application validation.

    It may also be possible in the future to generated customized message schemas that hardcode exactly which properties can appear by replacing the object-oriented nature of the reusable NIEM reference data model with flattened types for improved validation.

-------

# Utility schema changes

## Remove structures:sequenceID

[niemopen/niem-naming-design-rules#19](https://github.com/niemopen/niem-naming-design-rules/issues/19)

<!-- TODO: structures:sequenceID -->

- Replace with indicator that order matters
- new `appinfo:orderedPropertyIndicator` (or perhaps `appinfo:isOrdered`?)
- Appears in schemas?  In messages?

## Attribute wildcards

[niemopen/niem-naming-design-rules#20](https://github.com/niemopen/niem-naming-design-rules/issues/20)

The NTAC proposes to expand the use of attribute wildcards, currently used in NIEM to support security markup (IC-ISM and IC-NTK), to all NIEM-conformant and external attributes.  This would allow any schema-resolvable attribute to be attached to any NIEM property element in messages.  Message designers would have the ability to subset the wildcards down to only attributes within specific namespaces, or to remove the wildcard entirely if they wish to prevent the use of attributes in messages that are not declared in the message schemas.

While attributes can be added to existing types via augmentation or extension, attribute wildcards provide an easy means to extend existing data properties and datatypes with new attributes, which is otherwise not easy to do.

### Background

NIEM supports extensibility for objects via type extension, [augmentation](https://niem.github.io/reference/concepts/augmentation/), and [adapters](https://niem.github.io/reference/concepts/adapter/).  None of these solutions, however, work very well to support attaching additional NIEM-conformant or external attributes to existing NIEM data properties (properties that carry a value, like a string or a number or a boolean).

The NIEM [metadata](https://niem.github.io/reference/concepts/metadata/) mechanism can be used to link NIEM data properties to metadata objects that contain new attributes, but this can be a very cumbersome approach in message instances as it could require a metadata property counterpart for each NIEM data property that needs to carry custom attribute information.

#### Message example using metadata

```xml
<ext:Message
  xmlns:ext="http://www.example.com/wildcard-attributes/extension"
  xmlns:nc='http://release.niem.gov/niem/niem-core/5.0/'
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.example.com/wildcard-attributes/extension ./example.xsd">
  <nc:Person>
    <nc:PersonName>
      <nc:PersonGivenName structures:metadata="M1">...</nc:PersonGivenName>
      <nc:PersonSurName structures:metadata="M2">...</nc:PersonSurName>
    </nc:PersonName>
  </nc:Person>
  <nc:Address>
    <nc:AddressFullText structures:metadata="M3">...</nc:AddressFullText>
    <nc:AddressCityName structures:metadata="M4">...</nc:AddressCityName>
  </nc:Address>

  <nc:Metadata structures:id="M1" ext:transliterationText="..."/>
  <nc:Metadata structures:id="M2" ext:transliterationText="..."/>
  <nc:Metadata structures:id="M3" ext:transliterationText="..."/>
  <nc:Metadata structures:id="M4" ext:transliterationText="..."/>
</ext:Message>
```

#### Current NIEM attribute wildcards for security markup

NIEM currently leverages attribute wildcards from XML Schema to support the use of Intelligence Community Information Security Marking (ISM) and Need-to-Know (NTK) metadata attributes on any NIEM-conformant element.  These wildcards take the form of the `xs:anyAttribute` element on the following types and attribute group in `structures.xsd`:

- `structures:ObjectType`
- `structures:AssociationType`
- `structures:MetadataType`
- `structures:AugmentationType`
- `structures:SimpleObjectAttributeGroup`

##### Schema example: Attribute wildcards via xs:anyAttribute

The following is an extract from `structures.xsd`, showing the declaration of attribute wildcards on `structures:SimpleObjectAttributeGroup` and `structures:ObjectType`:

```xml
<xs:attributeGroup name="SimpleObjectAttributeGroup">
  <xs:annotation>
    <xs:documentation>A group of attributes that are applicable to objects, to be used when defining a complex type that is an extension of a simple type.</xs:documentation>
  </xs:annotation>
  <xs:attribute ref="structures:id"/>
  <xs:attribute ref="structures:ref"/>
  <xs:attribute ref="structures:uri"/>
  <xs:attribute ref="structures:metadata"/>
  <xs:attribute ref="structures:relationshipMetadata"/>
  <xs:attribute ref="structures:sequenceID"/>
  <xs:anyAttribute namespace="urn:us:gov:ic:ism urn:us:gov:ic:ntk" processContents="lax"/>
</xs:attributeGroup>

<xs:complexType name="ObjectType" abstract="true">
  <xs:annotation>
    <xs:documentation>A data type for a thing with its own lifespan that has some existence.</xs:documentation>
  </xs:annotation>
  <xs:sequence>
    <xs:element ref="structures:ObjectAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
  </xs:sequence>
  <xs:attribute ref="structures:id"/>
  <xs:attribute ref="structures:ref"/>
  <xs:attribute ref="structures:uri"/>
  <xs:attribute ref="structures:metadata"/>
  <xs:attribute ref="structures:relationshipMetadata"/>
  <xs:attribute ref="structures:sequenceID"/>
  <xs:anyAttribute namespace="urn:us:gov:ic:ism urn:us:gov:ic:ntk" processContents="lax"/>
</xs:complexType>
```

### Proposal

The NTAC proposes to expand the use of attribute wildcards to any defined schema source.  The value of the `processContents` attribute will be required to be "strict", meaning that a schema definition for the attributes must be resolvable and the attributes in messages must validate against the schema.

```xml
  <xs:complexType name="ObjectType" abstract="true">
    <xs:sequence>
      <xs:element ref="structures:ObjectAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="structures:id"/>
    <xs:attribute ref="structures:ref"/>
    <xs:attribute ref="structures:uri"/>
    <xs:attribute ref="structures:metadata"/>
    <xs:attribute ref="structures:relationshipMetadata"/>
    <xs:attribute ref="structures:sequenceID"/>
    <xs:anyAttribute namespace="##other" processContents="strict"/>
  </xs:complexType>
  ```

Notes:

- The value of the `namespace="##other"` attribute on `xs:anyAttribute` means that any attribute is allowed from any other namespace than the current one, in this case `structures.xsd`.
- The value of the `processContents="strict"` attribute on `xs:anyAttribute` means that the XML processor must be able to resolve the schema identified by the namespace URI and the attributes must be valid against that schema

#### Updated message example with attribute wildcards

```xml
<ext:Message
  xmlns:ext="http://www.example.com/wildcard-attributes/extension"
  xmlns:attr='http://www.example.com/wildcard-attributes/attributes'
  xmlns:nc='http://release.niem.gov/niem/niem-core/5.0/'
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="
    http://www.example.com/wildcard-attributes/extension ./example.xsd
    http://www.example.com/wildcard-attributes/attributes ./attributes.xsd">
  <nc:Person>
    <nc:PersonName>
      <nc:PersonGivenName attr:transliterationText="...">...</nc:PersonGivenName>
      <nc:PersonSurName attr:transliterationText="...">...</nc:PersonSurName>
    </nc:PersonName>
  </nc:Person>
  <nc:Address>
    <nc:AddressFullText attr:transliterationText="...">...</nc:AddressFullText>
    <nc:AddressCityName attr:transliterationText="...">...</nc:AddressCityName>
  </nc:Address>
</ext:Message>
```

Notes:

Attribute `attr:transliterationText` is allowed to appear in the message because:

1. It's namespace prefix is declared in the schema header (`xmlns:attr`)
2. The location of the attribute schema is provided via `xsi:schemaLocation`, which accepts one or more pairs of schema target namespace URIs and relative or absolute schema paths, each value and pair separated by whitespace.
3. The `transliterationText` attribute actual exists at the provided schema location.

#### Preventing unexpected or unwanted attributes in messages

The proposal with the attribute wildcard permits any attribute from any namespace other than structures to appear on any NIEM-conformant element.  Message designers may want to restrict the set of attributes that appear, or prevent them entirely.  This can be done by modifying the value of the `namespace` attribute on the `xs:anyAttribute` element in `structures.xsd` from "##other" to one or more space-delimited namespace URIs, or removing the `xs:anyAttribute` element entirely.

#### See more

See the [examples/attribute-wildcards](examples/attribute-wildcards/) subfolder for the wildcard example extension schema, the modified structures schema (`structures-modified.xsd`), the NIEM subset schemas updated to refer to the modified structures schema, and a valid and an invalid sample message.

## Disallow Direct Use of Structures Typing

[niemopen/niem-naming-design-rules#29](https://github.com/niemopen/niem-naming-design-rules/issues/29)

Disallow making elements directly from types defined in `structures`.

<!-- TODO: Add more text for disallow structures typing -->

### Impact

https://github.com/niemopen/niem-model/issues/24

`cbrn:CaseMetadata` and `cbrn:DataFileMetadata` are currently of type `structures:MetadataType`.  These two properties would need to be updated, perhaps to be of type `nc:MetadataType`.

-------

# TODO

Integrate the following notes into the document:

## Details from 2022-08-15 Face-to-Face

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

-------

# Appendix A. Informative References

<!-- Required section -->

This appendix contains the informative references that are used in this document.

While any hyperlinks included in this appendix were valid at the time of publication, OASIS cannot guarantee their long-term validity.

(Reference sources:
For references to IETF RFCs, use the approved citation formats at: \
https://docs.oasis-open.org/templates/ietf-rfc-list/ietf-rfc-list.html. \
For references to W3C Recommendations, use the approved citation formats at: \
https://docs.oasis-open.org/templates/w3c-recommendations-list/w3c-recommendations-list.html. \
Remove this note and the example reference below before submitting for publication.)

###### [CSAF-v2.0]
_Common Security Advisory Framework Version 2.0_. Edited by Langley Rock, Stefan Hagen, and Thomas Schmidt. Latest stage: https://docs.oasis-open.org/csaf/csaf/v2.0/csaf-v2.0.html

-------

# Appendix B. Acknowledgments

(Note: A Work Product approved by the TC must include a list of people who participated in the development of the Work Product. This is generally done by collecting the list of names in this appendix. This list shall be initially compiled by the Chair, and any Member of the TC may add or remove their names from the list by request.
Remove this note before submitting for publication.)

## B.1 Special Thanks

<!-- This is an optional subsection to call out contributions from TC members. If a TC wants to thank non-TC members then they should avoid using the term "contribution" and instead thank them for their "expertise" or "assistance". -->

Substantial contributions to this document from the following individuals are gratefully acknowledged:

Participant Name, Affiliation or "Individual Member"

## B.2 Participants

<!-- A TC can determine who they list here, however, TC Observers must not be listed. It is common practice for TCs to list everyone that was part of the TC during the creation of the document, but this is ultimately a TC decision on who they want to list and not list, and in what order. -->

The following individuals have participated in the creation of this document and are gratefully acknowledged:

**tc-full-name TC Members:**

| First Name | Last Name | Company |
| :--- | :--- | :--- |
Philippe | Alcon | Marvelous Networks
Alex | Amir | Viacat
Kris | Anders | Trend Mission
Darren | Anysteel | Macro Networks

-------

# Appendix C. Revision History
| Revision | Date | Editor | Changes Made |
| :--- | :--- | :--- | :--- |
| filename-v1.0-wd01 | yyyy-mm-dd | Editor Name | Initial working draft |

------

# Appendix D. Notices

Copyright &copy; OASIS Open 2023. All Rights Reserved.

All capitalized terms in the following text have the meanings assigned to them in the OASIS Intellectual Property Rights Policy (the "OASIS IPR Policy"). The full [Policy](https://www.oasis-open.org/policies-guidelines/ipr/) may be found at the OASIS website.

This document is published under [Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/legalcode).

All contributions made to this project have been made under the [OASIS Contributor License Agreement (CLA)](https://www.oasis-open.org/policies-guidelines/open-projects-process/#individual-cla-exhibit).

This document and translations of it may be copied and furnished to others, and derivative works that comment on or otherwise explain it or assist in its implementation may be prepared, copied, published, and distributed, in whole or in part, without restriction of any kind, provided that the above copyright notice and this section are included on all such copies and derivative works. However, this document itself may not be modified in any way, including by removing the copyright notice or references to OASIS, except as needed for the purpose of developing any document or deliverable produced by an OASIS Technical Committee (in which case the rules applicable to copyrights, as set forth in the OASIS IPR Policy, must be followed) or as required to translate it into languages other than English.

The limited permissions granted above are perpetual and will not be revoked by OASIS or its successors or assigns.

This document and the information contained herein is provided on an "AS IS" basis and OASIS DISCLAIMS ALL WARRANTIES, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO ANY WARRANTY THAT THE USE OF THE INFORMATION HEREIN WILL NOT INFRINGE ANY OWNERSHIP RIGHTS OR ANY IMPLIED WARRANTIES OF MERCHANTABILITY OR FITNESS FOR A PARTICULAR PURPOSE.

The name "OASIS" is a trademark of [OASIS](https://www.oasis-open.org/), the owner and developer of this specification, and should be used only to refer to the organization and its official outputs. OASIS welcomes reference to, and implementation and use of, specifications, while reserving the right to enforce its marks against misleading uses. Please see https://www.oasis-open.org/policies-guidelines/trademark/ for above guidance.
