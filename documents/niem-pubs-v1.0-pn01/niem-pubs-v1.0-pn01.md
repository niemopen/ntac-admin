
![OASIS Logo](https://docs.oasis-open.org/templates/OASISLogo-v3.0.png)
### OASIS Project Note
-------

# NIEMOpen Publication Process Version 1.0

## Project Note 01

## 26 January 2023

&nbsp;

#### This stage:
https://docs.oasis-open-projects.org/niem/niem-pubs/v1.0/pn01/niem-pubs-v1.0-pn01.md (Authoritative) \
https://docs.oasis-open-projects.org/niem/niem-pubs/v1.0/pn01/niem-pubs-v1.0-pn01.html \
https://docs.oasis-open-projects.org/niem/niem-pubs/v1.0/pn01/niem-pubs-v1.0-pn01.pdf

#### Previous stage:
N/A

#### Latest stage:
https://docs.oasis-open-projects.org/niem/niem-pubs/v1.0/niem-pubs-v1.0.md (Authoritative) \
https://docs.oasis-open-projects.org/niem/niem-pubs/v1.0/niem-pubs-v1.0.html \
https://docs.oasis-open-projects.org/niem/niem-pubs/v1.0/niem-pubs-v1.0.pdf

#### Open Project:
[NIEM Technical Architecture Committee (NTAC) of the OASIS NIEMOpen OP](http://www.niemopen.org/)

#### Project Chair:
Katherine Escobar (katherine.b.escobar.civ@mail.mil), [Joint Staff J6](https://www.jcs.mil/Directorates/J6-C4-Cyber/)

#### NTAC Committee Chairs:
Jim Cabral (jim.cabral@infotrack.com), [InfoTrack](https://www.infotrack.com/) \
Scott Renner (sar@mitre.org), [MITRE](https://www.mitre.org/)

#### Editors:
Jim Cabral (jim.cabral@infotrack.com), [InfoTrack](https://www.infotrack.com/) \
Christina Medlin (christina.medlin@gtri.gatech.edu), [GTRI](https://gtri.gatech.edu/)

#### Related work:
This document is related to:
[CTAS-v3.0](https://docs.oasis-open-projects.org/niem/ctas/v3.0/psd01/ctas-v3.0-psd01.html) Conformance Targets Attribute Specification (CTAS) Version 3.0. Edited by Tom Carlson. 17 January 2023. OASIS Project Specification Draft 01. ). [Latest stage](https://docs.oasis-open-projects.org/niem/ctas/v3.0/ctas-v3.0.html)

#### Abstract:
This document describes the terminology, deliverables, versioning, stages and processes involved in publishing a NIEMOpen version, including models, specifications and tools.

#### Status:
This is a Non-Standards Track Work Product. The patent provisions of the OASIS IPR Policy do not apply.

This document was last revised or approved by the Project Governing Board of the OASIS NIEMOpen OP on the above date. The level of approval is also listed above. Check the "Latest stage" location noted above for possible later revisions of this document. Any other numbered Versions and other technical work produced by the Open Project (OP) are listed at http://www.niemopen.org/.

Comments on this work can be provided by opening issues in the project repository or by sending email to the project’s public comment list: niemopen@lists.oasis-open-projects.org. List information is available at https://lists.oasis-open-projects.org/g/niemopen.

#### Citation format:
When referencing this document the following citation format should be used:

**[NIEM-Publication-v1.0]**

_NIEMOpen Publication Process Version 1.0_. Edited by James Cabral and Christina Medlin. 25 January 2023. OASIS Project Note 01. https://docs.oasis-open-projects.org/niem/niem-pubs/v1.0/pn01/niem-pubs-v1.0-pn01.html. Latest stage: https://docs.oasis-open-projects.org/niem/niem-pubs/v1.0/niem-pubs-v1.0.html.

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

Introductory material.

## 1.1 Glossary

### 1.1.1 Definitions of terms

- **NIEMOpen** (or **NIEM**) refers to a reference data model and related specifications.
- The NIEM **Core** refers to the content of the referance data model that is not associated with a specific Domain.
- A NIEM **Domain** refers to content of the reference data model that is associated with a specific segment of government or industry.

### 1.2.2 Acronyms and abbreviations

- **CMF**: Common Model Format
- **CTAS**: Conformance Target Attribute Specification
- **NMO**: NIEM Management Office TSC
- **NBAC**: NIEM Business Architecture Committee TSC
- **NTAC**: NIEM Technical Architecture Committee TSC
- **PGB**: Project Governance Board
- **TSC**: Technical Steering Committee

Note: NIEM is not a acronym

### 1.2.3 Document conventions

- Naming conventions
- Font colors and styles
- Typographic conventions
- Remove this section if unnecessary

### 1.3 Assumptions

- The NIEM Publication Process MUST comply with [OP requirements](https://www.oasis-open.org/policies-guidelines/open-projects-process/#progression-of-project-work) (see sections 11-14) including:
  - Emailing the [project distribution list](mailto:niemopen@lists.oasis-open.org) to announce any artifacts that require a vote of the PGB to approve (e.g. release/group releases, PSD, PS) at least 14 days in advance of the vote
  - Conforming PSDs and later to the PS template, which includes methods for indicating the relevant designated branches and applicable licenses
  - Ensuring any code included in a PS is composed only of Releases or Group Releases bearing Implementer-Class Licenses.
  - Ensuring any other content in a PS was contributed to a project repository
- The PGB SHOULD charter a Harmonization Subcommittee under the NBAC to review and recommend content changes to the model.

-------

# 2 Versions

NIEM versions will typically be published annually on a 3-year cycle.  A NIEM major version one year will be followed by minor versions the next two years.

## 2.1 Major Versions
Each NIEM major version:
- SHOULD include a reference data model, including any changes (additions, deletions, updates) to the model in previous version
- SHOULD include related specifications (e.g., Code Lists, Common Model Format, Conformance, Conformance Targents Attribute Specification, Message Specification, Naming and Design Rules, JSON binding, XML binding), including any changes (additions, deletions, updates) to the specifications in previous version
- SHOULD progress to OASIS Project Specification(s) (PS)
- MAY progress to OASIS Standard(s) and be submitted for ISO standard(s)

## 2.2 Minor Versions
Each NIEM minor version:
- MAY include updates to the reference data model in the previous version, including any changes (additions) to the Core or Domains
- MAY imclude updates to specifications in previous versions but SHOULD NOT include significant changes to the architecture 
- MAY address any defects in the data model or specifications in errata
- SHOULD progress to OASIS Project Specification(s) (PS)

-------
# 3 Project Artifacts

## 3.1 Published specifications
- Public review versions of project specifications MUST BE published at https://docs.oasis-open-projects.org/niem
- For URI permanency, all NIEMOpen artifacts should use https://docs.oasis-open-projects.org/niem as the root URI (e.g., in XML schema locations)
## 3.2 Project repositories
- Github repositories SHOULD be used primarily for sharing working versions of project artifacts with project members.
- All project repositories SHOULD reside under https://github.com/niemopen. e.g., 
  - Model repo: https://github.com/niemopen/niem-models
  - CTAS repo: https://github.com/niemopen/niem-conformance-targets
### 3.2.1 Branches
- Github branches SHOULD be used to segregate contributions that are not yet approved. e.g.,
  - `main`: approved specifications
  - `dev`: for incremental changes not yet approved
Temporary branches MAY be created as needed (ad hoc)
### 3.2.2 Tags
- Tags SHOULD be used to identify NIEM version and OASIS deliverable stage, e.g. `niem-6.0-ps` to 
## 3.3 XML Schemas
- XML schemas associated with the same NIEM version SHOULD use similar location URIs for maintainability
## 3.4 Approval of project artifacts
- When possible, TSCs SHOULD combine PGB notifications of related artifacts.
- TSCs SHOULD use permanent links to specific commits for approval by the PGB.
## 3.5 Legacy (pre-NIEMOpen) content and repositories      
- Previous NIEM models not yet contributed to the open project SHOULD continue to reside at https://release.niem.gov
- NIEM specifications not yet contributed to the open project SHOULD continue to reside at https://reference.niem.gov
- Pre-NIEMOpen repositories, Github and otherwise, MAY still be available but changes and new issues SHOULD NOT be accepted.
-------
# 4 Publication Processes
## 4.1 Publishing a minor version
This section describes the stages in publishing a minor version and the steps during each stage of ths process.
### 4.1.1 Designated Branch
This process begins the domain and other non-Core-related updates to the model:
1. Maintainers create a designated branch for a new version
2. Contributors create pull requests for each issue
 a. Each Domain Committee accepts changes to changes in their domain
 b. Harmonization Subcommittee accepts changes to:
   - Non-domain code tables, adapters and external standards, Core supplements, etc.
   - Other content if the authority (e.g. Domain Committee) is unresponsive
   - Harmonization Committee reviews content and accepts changes to harmonize domains and Core
### 4.1.2 Release
Once all major content changes have been submitted:
1. Contributors continue to create pull requests (as above)
2. TSCs review the designated branch and contribute changes (as needed)
3. Once all TSCs approve the changes, maintainers add the appropriate tag (e.g., `niem-6.0-release1`) and publish as a Github release
4. TSCs announce release to the [project distribution list](mailto:niemopen@lists.oasis-open.org) for a 14 day review period
5. Contributors submit feedback via GitHub issues and pull requests
6. Maintainers review feedback, continuing to get TSC approval before accepting pull requests
7. TSCs review the release and request approval of additional releases (as needed)
### 4.1.3 Project Specification Draft
Once all known changes have been submitted for the version and the TSCs are satisfied with the release:
1. TSCs announce on the [project distribution list](mailto:niemopen@lists.oasis-open.org) their recommendation that the PGB approve the release as a PSD
2. If the PGB approves the PSD,
 a. Maintainers add the appropriate tag (e.g., `niem-6.0-psd1`) and publish as a Github release
 b. TSCs announce the PSD to the [project distribution list](mailto:niemopen@lists.oasis-open.org) for a 14 day review period
 c. Contributors submit feedback via GitHub issues and pull requests
 d. Maintainers review feedback, continuing to get TSC approval before accepting pull requests
 e. TSCs review the release and request approval of additional PSDs (as needed)
### 4.1.4 Project Specification
Once the TSCs are satisfied with the PSD:
1. TSCs announce on the [project distribution list](mailto:niemopen@lists.oasis-open.org) their recommendation that the PGB approve the PSD as a PS
2. If the PGB approves the PS (by Special Majority Vote),
 a. Maintainers add the appropriate tag (e.g., `niem-6.0-ps1`) and publish as a Github release
 b. TSCs announce the PS to the [project distribution list](mailto:niemopen@lists.oasis-open.org)
 c. Open Project Administrators publish the PS on https://docs.oasis-open-projects.org/niem
## 4.2 Publishing a major version
The process of publishing a major version will be similar to [4.1 Publishing a NIEM minor versions](#4.1-publishing-minor-version), with the following additional steps to the Designated Branch stage.
### 4.2.1 Designated Branch
5. Harmonization Subcommittee reviews Core and Core-related pull requests
6. NBAC Harmonization Subcommittee meets regularly for harmonization work
7. NTAC reviews architectural-related pull requests for the model
8. NTAC updates the related specifications

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
Remove this note before submitting for publication.)

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
Jim | Cabral | InfoTrack US
Tom | Carlson | GTRI
Christina | Medlin | GTRI
Scott | Renner | MITRE
Update with the full TSC roster before publishing

-------

# Appendix C. Revision History
| Revision | Date | Editor | Changes Made |
| :--- | :--- | :--- | :--- |
| niem-pubs-v1.0-pn01 | 2023-01-26 | Jim Cabral | Initial working draft |

------

# Appendix D. Notices

Copyright &copy; OASIS Open 2023. All Rights Reserved.

All capitalized terms in the following text have the meanings assigned to them in the OASIS Intellectual Property Rights Policy (the "OASIS IPR Policy"). The full [Policy](https://www.oasis-open.org/policies-guidelines/ipr/) may be found at the OASIS website.

This document and translations of it may be copied and furnished to others, and derivative works that comment on or otherwise explain it or assist in its implementation may be prepared, copied, published, and distributed, in whole or in part, without restriction of any kind, provided that the above copyright notice and this section are included on all such copies and derivative works. However, this document itself may not be modified in any way, including by removing the copyright notice or references to OASIS, except as needed for the purpose of developing any document or deliverable produced by an OASIS Technical Committee (in which case the rules applicable to copyrights, as set forth in the OASIS IPR Policy, must be followed) or as required to translate it into languages other than English.

The limited permissions granted above are perpetual and will not be revoked by OASIS or its successors or assigns.

This document and the information contained herein is provided on an "AS IS" basis and OASIS DISCLAIMS ALL WARRANTIES, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO ANY WARRANTY THAT THE USE OF THE INFORMATION HEREIN WILL NOT INFRINGE ANY OWNERSHIP RIGHTS OR ANY IMPLIED WARRANTIES OF MERCHANTABILITY OR FITNESS FOR A PARTICULAR PURPOSE.

The name "OASIS" is a trademark of [OASIS](https://www.oasis-open.org/), the owner and developer of this specification, and should be used only to refer to the organization and its official outputs. OASIS welcomes reference to, and implementation and use of, specifications, while reserving the right to enforce its marks against misleading uses. Please see https://www.oasis-open.org/policies-guidelines/trademark/ for above guidance.
