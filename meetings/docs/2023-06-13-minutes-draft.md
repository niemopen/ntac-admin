# 2023-06-13 NTAC TSC Call Minutes

1. Review and approve minutes: [2023-06-06-minutes-draft.md](2023-06-06-minutes-draft.md)
	- Approved without change
2. NDR Overhaul
3. Understanding the NIEM Information Model
4. NIEM to Incorporate other standards by reference
5. Wildcard Augmentations
6. Technical Architecture Project Note
7. Tools and Software Build Automation
8. CMFTool
9. New Business

___

### NDR Overhaul

- [NDR Principles discussion board](https://github.com/niemopen/ntac-admin/discussions/38)
- "What's New" document
	- Called "NIEM 6.0 Architectural Changes Project Note"
	- https://github.com/niemopen/ntac-admin/blob/main/project-notes/docs/niem-6.0-arch-changes/niem-6.0-arch-changes-v1.0-pn01.md
- `nc:ObjectType` not in there yet, okay to add? (See discussion below.)
- Metadata applicability to parent section, does it go away?
- Removing `structures:@sequenceID` for `OrderedPropertyIndicator`, default is unordered
	- apply by modifying user schemas
		- goes on the element `ref`
		- need to add to the tooling
	- Do we still need something for names?
	- Should that be something the message spec developer adds to meet their needs?
	- (See earlier discussions below)
- [ ] All, review this week to decide whether to publish next week
- what do we call a complex type with simple content?
	- [[Jim Cabral]] call it "complex type with simple content"
	- [[Christina Medlin|CM]] "complex value type"

### CMFTool

- How to constrain different things of the same type?

	- CMF doesn't have that
	- Can do at the XML/JSON Schema level
	- Would have to greatly complicate CMF in order to replicate this simplification
	- Push direct CMF support to NIEM 7.0


## NDR and External Standards

Proposal to allow NIEM to incorporate other standards by reference, in addition to the current inclusion via adapters

- Not much demand for JSON standards in the past, but now is building, e.g. [[STIX]]

Issues/Feature Requests:

1.  Adding support for external JSON standards, including JSON adapters.
2.  Adding support for linked data between JSON and XML NIEM-conforming messages
3.  Enabling contributors to submit suggest content in a JSON format

[[Jim Cabral]] Shouldn't these requirements come from the NBAC?
[[Scott Renner|Dr. Scott]] For JSON standards, it'll be palatable, take their standard, turn into CMF, spit out XSD

[[Tom Carlson]] Three levels?

1. Convert and incorporate
2. Connect with adapters
3. Reference, no validation

#resolved Kick this to the NBAC for their feedback on the requirements and priority, [[Christina Medlin|CM]] to bring to them

### `nc:ObjectType`?

https://github.com/niemopen/ntac-admin/discussions/58

Need a universal base type for ISM attributes. Add `ObjectType` into niem-core so it can get into the CMF without including `structures` itself? (Because `structures` is XML-specific plumbing.)

[[Christina Medlin|CM]] Could make it a conformant type?
[[Jim Cabral]] Big change that impacts everything, so we need a _really good_ reason to make the change.

- Need 2, `ObjectType` and `AssociationType`
- Assoc is already in niem-core
- ObjectType just contains the aug point
	- Which won't show up in JSON, as it doesn't do the abstracts
- Still in `structures`
	- `MetadataType`
		- [[Christina Medlin|CM]] Need to figure this out for the What's New
		- [[Scott Renner|Dr. Scott]] First draft can go out without this
	- `AugmentationType`

### `@sequenceID`

Which is the default? Ordered or unordered?
[[David Kemp|Dave Kemp]]: JSON arrays are ordered.
[[Christina Medlin|CM]]: We've flip-flopped on this
[[Jim Cabral]]: Need a means of ordering, regardless of which we pick.

What's the original rationale for this? It's an XML kludge. Doesn't work well in RDF.

[[Christina Medlin|CM]]: Name ordering needs the attribute, because the ordering can change.
[[Scott Renner|Dr. Scott]]: Display order is something you model.


