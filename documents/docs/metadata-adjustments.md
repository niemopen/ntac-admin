
# Metadata Adjustments for NIEM 6.0

## Content changes

If the current metadata proposal is adopted for NIEM 6.0, the following changes content and NDR rules changes may need to be made.  New NDR rules may still need to be identified.

### Generic metadata

- **`nc:Metadata`** (`nc:MetadataType`) - Leave as is.
- **`hs:Metadata`** (`hs:MetadataType`) - Convert to an augmentation of `nc:MetadataType`? #resolved to recommend this
- **`j:Metadata`** (`j:MetadataType`) - Convert to an augmentation of `nc:MetadataType`? #resolved to recommend this

`nc:Metadata` is the _one_ generic metadata object

`applies-to-types`? Does it go away?

#resolved `applies-to-types` goes away

### CBRN metadata

**`cbrn:CaseMetadata`** (`structures:MetadataType`) - Add to `cbrn:CBRNECaseType`? #resolved Recommend adding

**`cbrn:DataFileMetadata`** (`structures:MetadataType`) - Add to `cbrn:DataFileType`? #resolved Recommend adding

**`cbrn:TotalDoseMetadata`** (`cbrn:TotalDoseMetadataType`) and **`cbrn:TotalExposureMetadata`** (`cbrn:TotalExposureMetadataType`):

- Currently applies to `structures:ObjectType`.
- Seems likely to apply to some simple content elements.
- #resolved Recommend leaving as is.

### CUI metadata

**`cui:DocumentMarkingMetadata`**

- Add to new `cui:DocumentAugmentationType` and new `cui:ReportAugmentationType`?
- Users can add to their own IEPD root type if it does not derive from one of these.
- Leave the metadata object itself as is
- #resolved Recommend this

**`cui:PortionMarkingMetadata`**

- Leave as is.
- If IEPD developers want to be able to portion mark any object, they can pretty easily add it to their own `structures:ObjectType` augmentation.
- #resolved Recommend this

### MilOps metadata

The following metadata elements are of type `nc:MetadataType` and may not be necessary, but are used in JNKE. (May not be in the latest or last version):

- **`mo:AnalyticNoteMetadata`** (`nc:MetadataType`)
- **`mo:CETMetadata`** (`nc:MetadataType`)
- **`mo:ProvenanceMetadata`** (`nc:MetadataType`)
- **`mo:UnitMetadata`** (`nc:MetadataType`)
- **`mo:WCMMetadata`** (`nc:MetadataType`)

**`mo:ImplementationSpecificSettingMetadata`** (`mo:ImplementationSpecificSettingMetadataType`)

- Add to `mo:SettingDataType` #resolved Recommend this

**`mo:UncertaintyMetadata`** (`mo:UncertaintyMetadataType`)

- Add to new `mo:MeasureAugmentationType`

**`mo:MinimumEssentialMetadata`** (NIEM 5.2)

- Similar to `cui:DocumentMarkingMetadata`, add to new `mo:DocumentAugmentationType` and new `mo:ReportAugmentationType` as these are common types from which to extend an IEPD root type.
- Users can add this metadata element directly to their own IEPD root type if it does not already extend from `nc:DocumentType` or `nc:ReportType`.
- #resolved Recommend this

### Screening metadata

**`scr:PersonMetadata`** (`scr:PersonMetadataType`)

- Currently applies to `nc:PersonType`; add to new `scr:PersonAugmentationType`?
- #resolved Recommend this

## NDR metadata rules

### REF and EXT text rules

- [10-1](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_10-1) - Complex type has a category
	- Okay as is
- [10-38](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_10-38) - Metadata type has data about data
	- Okay as is
- [10-41](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_10-41) - (also SET) Metadata element has applicable elements `appinfo:appliesToElements` and `appinfo:appliesToTypes`
	- Simplify and drop this rule?  Could still use this for the CBRN simple content use case, but seems like a lot of overhead. (And they don't use it now anyway.)
- #resolved Recommend this. As far as we know, no one uses this. Community can push back if they need it.

### REF and EXT Schematron rules

- [10-39](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_10-39) - Metadata types are derived from `structures:MetadataType`
	- Rule seems to permit extension from metadata types, not just `structures:MetadataType`, contrary to what we've been saying
	- Nothing to do here, the wording now fits what we want
- [10-40](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_10-40) - Metadata element declaration type is a metadata type

- [10-72](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_10-72) - `appinfo:appliesToTypes` annotates metadata element
	- Drop this rule?
	- Drop `appinfo:appliesToTypes`?
	- #resolved recommend this

- [10-74](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_10-74) - `appinfo:appliesToElements` annotates metadata element
	- Drop this rule?
	- Drop `appinfo:appliesToElements`?
	- #resolved recommend this

- [11-33](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_11-33) - Standard opening phrase for metadata element data definition
	- Use the first standard opening phrase? "Metadata about..."
	- #resolved Standardize on "Metadata about..."

Current options:

- Metadata about...
- Information that further qualifies...

- [11-45](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_11-45) - Standard opening phrase for metadata type data definition

- Use the first standard opening phrase?

Current options:
- A data type for metadata about...
- A data type for information that further qualifies...
- #resolved Standardize on "A data type for metadata about..."

### INS text rules

- [12-11](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_12-11) - Metadata applies to referring entity
	- Update rule to include metadata applies to the object in which it is contained if no `structures:id` or `structures:uri` attribute exists for it
	- #resolved Do this
- [12-12](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_12-12) - Referent of `structures:relationshipMetadata` annotates relationship
	- Leave as is

**Schematron to be examined**

- [12-13](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_12-13) - Values of `structures:metadata` refer to values of `structures:id`

> Given that each IDREF in the value of an attribute `structures:metadata` must match the value of an ID attribute on some element in the XML document, that ID attribute MUST be an occurrence of the attribute `structures:id`.

- Drop rule?  See 12-16.

[12-14](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_12-14) - Values of `structures:relationshipMetadata` refer to values of `structures:id`

> Given that each IDREF in the value of an attribute `structures:relationshipMetadata` must match the value of an ID attribute on some element in the XML document, that ID attribute MUST be an occurrence of the attribute `structures:id`.

- Drop rule?  See 12-17.

[12-15](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_12-15) - `structures:metadata` and `structures:relationshipMetadata` refer to metadata elements

> Each element referenced by an attribute `structures:metadata` or an attribute `structures:relationshipMetadata` MUST have element declaration that is a metadata element declaration.

- Drop rule?  See INS Schematron rule 12-16 and 12-17 below.

[12-18](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_12-18) - Metadata is applicable to element

- Update rule?  Talks about applicability and a `$SUBJECT-ELEMENT` (unrelated child).
- No longer makes sense along with 10-41
- #resolved looks like it's no longer applicable

### INS Schematron rules

[12-16](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_12-16) - Attribute `structures:metadata` references metadata element

> Each item in the value of an attribute `structures:metadata` MUST appear as the value of an attribute `structures:id` with an owner element that is a metadata element.

- Keep rule, but does this Schematron rule make text rules 12-13 and 12-15 unnecessary?
- Change "element" in rule title to "element(s)"?
- Add extra explanatory text if dropping the text rules?

[12-17](https://niem.github.io/NIEM-NDR/v5.0/niem-ndr.html#rule_12-17) - Attribute `structures:relationshipMetadata` references metadata element

> Each item in the value of an attribute `structures:relationshipMetadata` MUST appear as the value of an attribute `structures:id` with an owner element that is a metadata element.

- Keep rule, but does this Schematron rule make text rules 12-14 and 12-15 unnecessary?
- Change "element" in rule title to "element(s)"?
- Add extra explanatory text if dropping the text rules?
