# 2023-06-06 NTAC TSC Call Minutes

1.  Review and approve minutes: [2023-05-30-minutes-draft.md](2023-05-30-minutes-draft.md)
	- Approved without changes
2.  Wildcard Augmentations
3.  Technical Architecture Project Note
4.  Understanding the NIEM Information Model 
5.  NIEM to Incorporate other standards by reference
6.  Tools and Software Build Automation
7.  CMFTool
8.  New Business

### Wildcard Augmentations

- **No update while Dr. Renner is out**
- [Wildcard Augmentations discussion board](https://github.com/niemopen/ntac-admin/discussions/32)
- Status of next CMF version:
	- Will have attribute wildcards, but not element ones.
	- Augmentation instead of metadata attributes
- [ ] Dr. Renner to incorporate latest changes in the CMFTool
	- Discussion page in examples repo contains examples

### Technical Architecture Project Note

- Current status
	- Working Draft in the repo
		- https://github.com/niemopen/ntac-admin/tree/main/project-notes/docs/tech-arch-v1.0-pn01
		- Still need to pull principles into it
	- No new content or changes
	- Not a big priority, doesn't block the release

### Understanding the NIEM Information Model

Related document from Dave Kemp, [Understanding the NIEM Information Model](https://github.com/niemopen/ntac-admin/tree/main/project-notes/docs/information-model-v1.0-pn01)
	- [ ] All, read and discuss
	- Deals with difference between info modeling and data modeling
	- Motivation for supporting that approach
	- Potential for potential future roadmap work
		- Information equivalence
		- RDF isn't info modeling
		- JADN is the suggested mechanism
	- [ ] Dave Kemp to rename to clarify role

### NDR Overhaul

- [NDR Principles discussion board](https://github.com/niemopen/ntac-admin/discussions/38)
- "What's New" document
	- Called "NIEM 6.0 Architectural Changes Project Note"
	- https://github.com/niemopen/ntac-admin/blob/main/project-notes/docs/arch-changes-v1.0-pn01/arch-changes-v1.0-pn01.md
	- Christina Medlin finishing up this week, for review next week

### NDR Changes

- Two small textual changes:
	1. [Duplicate Rule Titles (5-1, 12-7)](https://github.com/niemopen/niem-naming-design-rules/issues/1)
	2. [Fix wording in deprecation section](https://github.com/niemopen/niem-naming-design-rules/issues/2)
- #resolved to accept these changes to the NDR

### Software Build Automation

- **No update while Dr. Renner is out**

### CMFTool

- **No update while Dr. Renner is out**

### Role Revamp

- **No updates**

## NDR and External Standards

- Proposal to allow NIEM to incorporate other standards by reference, in addition to the current inclusion via adapters
- Not much demand for JSON standards in the past, but now is building, e.g. STIX
	- Need JSON adapters?
- Is there now a need to incorporate JSON messages?
	- XML and JSON as separate messages, but need to reference each other?
	- Linked data between JSON and XML NIEM-conforming messages?
- Or just that JSON messages can reference JSON standards?
- Just a URI to point?

- Related: Enabling contributors to submit suggest content in a JSON format
	- JSON Schema or JSON instance as example?

#adjourned 
