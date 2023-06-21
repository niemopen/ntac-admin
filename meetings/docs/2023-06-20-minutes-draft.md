# 2023-06-20 NTAC TSC Call Minutes

1. Review and approve minutes: [2023-06-13-minutes-draft.md](2023-06-13-minutes-draft.md)
	- Approved without change
2. NDR Overhaul
	- Updates to the "What's New"
3. Understanding the NIEM Information Model
4. NIEM to Incorporate other standards by reference
	- Harmonization and JSON External Standards
5. Wildcard Augmentations
6. Technical Architecture Project Note
7. Tools and Software Build Automation
8. CMFTool
9. New Business

## NDR What's New

- Overview and specifics could be combined in some way
- Can kill 7 Metadata Changes, because enhanced augmentations fix, see 2.9
- Need to disambiguate EXT schema vs message schema
	- Define EXT now for this document
	- [Ref vs Ext](https://niem.github.io/reference/specifications/ndr/ref-vs-ext/)
- Issues with section 2.2, assigning NIEM subset schemas the EXT conformance target
	- This is a hack
	- Adding conditionals to validation is a hack
	- Do it right by making a new SUB conformance target

### Action Items for next week

- [ ] Fix the section numbering [[Tom Carlson]]
- [ ] Clarify what EXT means [[Jim Cabral]]
- [ ] Fix 2.2 to add SUB conformance target [[Jim Cabral]]
	- at this point just say we will, not the specifics
- [ ] 2.4 invalidates 9.2, align 9.2 with 2.4 [[Christina Medlin]]
- [ ] 2.8 Pull `nc:AssociationType` as it already exists, if not new [[Tom Carlson]]
- [ ] Remove 2.9 for now pending further discussion [[Scott Renner]]

## NDR and External Standards

### Harmonization Committee

- Presented [[Duncan Sparrell]] proposal
- They wanted this support, JSON external standards
- What does "by reference" mean?
- They're fine with adapters
- Are there examples?
	- [[STIX]]
	- [[SPDX]] and CycloneDX
	- CACAO
	- OpenC2
	- IOB
	- Threat Actor Context
	- NIEM model issues 28 thru 33
	- Can we incorporate STIX without adapter types?
- Add "XML" and "JSON" to the adapter names to differentiate?
	- Need examples
	- For `Whatever`, have `WhateverXMLAdapter` and `WhateverJSONAdapter`
	- This might cause problems

## New Business

- [[Lavdjola Farrington]]: Teams vs Zoom
	- NMO is moving to Zoom
	- Some NTAC can't access Zoom
	- Other options include WebEx, JITSI, BlueJeans
	- #resolved Let the NMO try it out first

