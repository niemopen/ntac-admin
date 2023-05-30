# 2023-05-23 NTAC TSC Call Agenda
[NTAC calendar](https://lists.oasis-open-projects.org/g/niemopen-ntactsc/calendar)

[NTAC meeting link on Teams](https://dod.teams.microsoft.us/l/meetup-join/19%3adod%3ameeting_027b8f8cd305438fbb0a76a1e7896d97%40thread.v2/0?context=%7b%22Tid%22%3a%22102d0191-eeae-4761-b1cb-1a83e86ef445%22%2c%22Oid%22%3a%2270ae69c4-ba53-4071-b60d-68a8b321854e%22%7d)


1.  Review and approve minutes: [2023-05-16 minutes (draft)](https://github.com/niemopen/ntac-admin/blob/main/meetings/docs/2023-05-16-minutes.md)
	- Approved without change
2.  Publication Process Project Note
3.  Wildcard Augmentations
4.  Technical Architecture Project Note
5.  Tools and Software Build Automation
6.  CMFTool
7.  NIEM to Incorporate other standards by reference
8.  New Business

## Working Notes

### Publication Process Project Note
	- #resolved close the issue & remove from future agendas

### Wildcard Augmentations
 - No new update 

### Technical Architecture Project Note

- Current status
		- [X] Move into own section on the agenda

### NDR Overhaul

- [NDR Principles discussion board](https://github.com/niemopen/ntac-admin/discussions/38)
- "What's New" document
	- Called "NIEM 6.0 Architectural Changes Project Note"
	- https://github.com/niemopen/ntac-admin/blob/main/project-notes/docs/arch-changes-v1.0-pn01/arch-changes-v1.0-pn01.md
	- Target date for the release has been delayed to allow Dr. Scott to weight in, aiming for June 9th or the week after. 
		
	

### Software Build Automation

- [Digital Engineering discussion board](https://github.com/niemopen/ntac-admin/discussions/41)
  - No new update. 

### CMFTool

- Status?
- Prior status
	- CMF in NIEM 6.0 format
	- XML Schema in 5.0 and 6.0 formats
	- Close to working
- [ ] July 10th deadline for CMFTool 0.7 beta that generates CMF and XML models in NIEM 6.0

### Role Revamp

- https://github.com/niemopen/niem-naming-design-rules/issues/6

#resolved Working assumption is to adopt [[Christina Medlin|CM]] recommendation

#adjourned 


## New Business

### Additional Informational Content

[[Duncan Sparrell]]:

1.  The directory structure in [https://github.com/niemopen/niem-model](https://github.com/niemopen/niem-model "https://github.com/niemopen/niem-model"). If you look at the readme now, it explains the difference between 5.1 and 5.2. It doesn’t explain what is in the csv directory vs the json-ld directory vs the xlsx directory vs the xsd directory.
2.  The directory structure in [https://github.com/niemopen/niem-model/tree/main/xsd](https://github.com/niemopen/niem-model/tree/main/xsd "https://github.com/niemopen/niem-model/tree/main/xsd"). This directory doesn’t even have a readme (albeit it would be ok if explained in it’s parent readme)

### NDR and Codes

- [[Christina Medlin|CM]] Do codes both ways, with the simple type still available for unioning
	- In reference schema? Or just in extensions?
- [[Jennifer Stathakis]] Fingerprinting codes combined in different ways as valid
- Unique codes is Issue #26
- Current thinking is:
	- Still support simple types for unioning
	- Keep in references
	- Allow different rules for message schemas
	- Require unique codes
		- Fix up code tables that have dupes

### `nc:ObjectType`?

https://github.com/niemopen/ntac-admin/discussions/58

Need a universal base type for ISM attributes. Add `ObjectType` into niem-core so it can get into the CMF without including `structures` itself? (Because `structures` is XML-specific plumbing.)

[[Christina Medlin|CM]] Could make it a conformant type?
[[Jim Cabral]] Big change that impacts everything, so we need a _really good_ reason to make the change.

### `@sequenceID`

Which is the default? Ordered or unordered?
[[David Kemp|Dave Kemp]]: JSON arrays are ordered.
[[Christina Medlin|CM]]: We've flip-flopped on this
[[Jim Cabral]]: Need a means of ordering, regardless of which we pick.

What's the original rationale for this? It's an XML kludge. Doesn't work well in RDF.

[[Christina Medlin|CM]]: Name ordering needs the attribute, because the ordering can change.
[[Scott Renner|Dr. Scott]]: Display order is something you model.



