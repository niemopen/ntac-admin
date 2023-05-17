# NIEM 6.0 Architectural Changes Project Note (i.e. What's New in NIEM 6.0)

## New Content

- TBD

## Introduction of CMF

- CMF as a peer canonical format for NIEM 6.0, alongside XML Schema
- Planned to replace XML Schema as the sole canonical form in NIEM 7.0

## Simplified Roles

- **Potential**
- Replace separate Role objects linked via `@id` and `@ref` with `@uri` aggregating multiple objects into one conceptual object

## Removal of @sequenceID

- Replace with indicator that order matters
- new `appinfo:orderedPropertyIndicator` (or perhaps `appinfo:isOrdered`?)

## Metadata Changes

- No metadata types or attributes
- Attribute wildcards, but not element wildcards
- Augmentation replacing metadata attributes
- Use augmentation to add a metadata object to complex content
- Use a reference attribute to add a metadata object to simple content
- Applying metadata to simple content will require the user to create the appropriate object
	- Simple content no longer has attributes unless you put them there on purpose
	- which is discouraged, because it makes NIEM JSON more complicated.


### Details from 2022-08-15 Face-to-Face

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

## Representation Terms

- Add "Adapter" as a representation term
	- https://github.com/NIEM/NIEM-NDR/issues/114

## Disallow Direct Use of Structures Typing

- **Potential**
- Disallow making elements directly from types defined in `structures`

## Unique Enums

- Require unique enumerations in code tables
- Fix any submitted tables with duplicates by adding all the definitions to the enum

## XSD Conformance Targets

- ReferenceSchema – has open content (attribute wildcards in structures, for augmentations)
- SubsetSchema - now marked with EXT conformance target to minimize conformance issues
- ExtensionSchema – like ReferenceSchema but `xs:any` is OK
- MessageSchema – for validation & binding, not for model semantics
	- Closed content (no attribute wildcards)
	- `xs:choice` OK
	- local type definitions OK
	- local element and attribute declarations OK

## Utility Namespace Changes


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

## Other

- Relative URIs are blank nodes in messages with no base URI.

- `@xml:lang` only applies to elements with a string-valued CSC that includes `@xml:lang`
	- Doesn't apply to attribute values at all.

