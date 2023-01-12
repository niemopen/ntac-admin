
# NIEM Open Deliverable Strategy

January 9, 2023

## Terminology

- [ ] NIEM Open (or NIEM) will collectively refer to the model and specifications
- [ ] NIEM Open will have one model that will have multiple versions (will need to rename the new models repo)
- [ ] NIEM Open will have seven specifications (NDR, NIEM JSON, Message Spec, Code Lists, Conformance, CTAS, CMF), each of which may have one or more versions
- [ ] NIEM Open major version (or NIEM major version) will replace a NIEM Classic major release
- [ ] NIEM Open minor version (or NIEM minor version) will replace a NIEM Classic minor release
- [ ] Deliverable cycle will replace release cycle to avoid conflicts with OASIS releases

## Deliverable cycle

### NIEM Classic

- Annual releases on a 3-year cycle.  A major release one year will be followed by minor releases the next two years.
- Major releases
  - permit content changes to Core, domains, code tables, and any other namespace
  - permit architectural changes, including changes to utility schemas (e.g., `structures.xsd`), typically specified by the NIEM Naming and Design Rules (NDR)
  - coincide with updates to specifications, also released around the same time
- Minor releases
  - permit only content changes to namespaces that are not imported by Core, including domains and code tables
  - Core and utility schemas are locked (Core can only be updated every 3 years)
  - Architecture is locked; specifications are not updated unless additive (rare) or they do not affect the model or IEPDs
- Core supplements and domain updates
  - can be published outside of the release cycle to additively provide new or updated content
  - these changes are published in new schemas that can be used in addition to the original release schemas
  - Core supplements can only be integrated into the next major release
  - domain updates can be integrated into the next release (major or minor)

### NIEM Open

- [ ] Annual versions of NIEM on a 3-year cycle.  A major version of the model and specifications one year will be followed by minor versions the model the next two years.
- [ ] Major versions of the NIEM (model and specifications)
  - [ ] may include any changes (additions, deletions, updates) to the model or specifications
  - [ ] will progress to OASIS Project Specification (PS)
  - [ ] may progress to OASIS standard and be submitted for ISO standard
- [ ] Minor versions of the NIEM model
  - [ ] may include backward-compatible changes (additions) to the content in the Core or domains
  - [ ] may not include changes to the architecture 
  - [ ] any defects in the architecture or content will be addressed in errata
  - [ ] will progress to OASIS project specifications (PS)

## Publication

### NIEM Classic

- NIEM releases are published at https://release.niem.gov
  - Target namespaces of NIEM schemas resolve here with the help of redirects
- NIEM specifications are published at https://reference.niem.gov
  - Conformance target identifiers in the specs use this url as their base
- NIEM Core supplements and domain updates are published at https://publication.niem.gov
- NIEM began to also publish releases and specifications in parallel to GitHub starting with NIEM 4.0 (2017).
  - https://github.com/NIEM/NIEM-Releases/
  - https://github.com/NIEM/NIEM-NDR/, etc.
  - Branching
    - `master` is used for final releases
    - `dev` is used for incremental changes
      - Separate branches were originally used for the development of different releases, with the final release products being merged (squashed) into the `master` branch at the end.
      - This was simplified to reduce confusion and to follow common best practices that recommend using development and feature branches.
    - ad hoc feature branches as needed
    - Tags are used to identify releases (e.g., niem-5.1) and drafts (e.g., niem-5.1alpha1)
      - Provides a persistent anchor to specific commits
      - Used `niem` for releases (e.g., `niem-5.0`), specifications used their own labels (e.g., `ndr-5.0`)
  - All previous releases and specification versions were committed in order before 4.0 was posted

### NIEM Open

- [ ] Project artifacts
  - [ ] Publish public review versions of project artifacts at https://docs.oasis-open.org/niem
  - [ ] Use https://docs.oasis-open.org/niem as the root URI of NIEM Open artifacts
  - [ ] Maintain https://release.niem.gov as the root URI for pre-OASIS artifacts 
- [ ] Project repositories
  - [ ] Publish working versions (for use by project members) of project artifacts on Github repositories
  - [ ] Naming
    - [ ] Repositories
      - [ ] Model: https://github.com/niemopen/niem-model
      - [ ] Specifications: e.g., https://github.com/niemopen/niem-naming-design-rules
    - [ ] Branches
      - [ ] `main` branch for final deliverable products
      - [ ] `dev` branch for incremental changes
      - [ ] ad hoc feature branches as needed
    - [ ] Versions
      - [ ] Use tags, e.g. `v6.0-ps` to identify NIEM version and OASIS deliverable stage
  - [ ] Archive non-OASIS Github repositories - content will still be available, but changes and new issues would no longer be able to be posted.
  - [ ] Contribute and commit existing content to NIEM Open GitHub repos
    - [ ] Commit NIEM 1.0 - 5.2 models to the niem-model repository
    - [ ] Commit NIEM 1.0 - 5.2 specifications to the appropriate project repositories
- [ ] Schemas
  - [ ]  Sync schema location uris with the NIEM version for maintainability

## Deliverable stages

### NIEM Classic

#### Alpha(s)

- Substantial new changes are due.
- Goes out for semi-internal review.  Committees and domains are notified and can be shared with whomever they wish, posted publicly on GitHub, but no broad community announcement.

#### Beta(s)

- Smaller changes, code table updates, harmonization, bug fixes.
- Goes out for two-week public review.

#### Release candidate(s)

- Bug fixes.  New release candidates are published until there is no feedback, and then that last release candidate will be used for the final release.
- Initial release candidate goes out for a 1-week public review.  Subsequent RCs are posted publicly, but only require confirmation

#### Release

- Finalized product.  Changes can only be made through supplement schemas.

### OASIS

Full requirements are at https://www.oasis-open.org/policies-guidelines/open-projects-process/#progression-of-project-work (see sections 11-14)

#### Designated branch -> Project Release

- Notify all contributors at least 14 days prior to the PGB vote
- Instead of an in-meeting vote, can hold a call for objections over email (needs 3-day window)

#### Project releases -> Group Release

- Notify all contributors at least 14 days prior to the PGB vote
- 3-day email call for objections period

#### Project or Group Release -> Project Specification Draft (PSD)

- Notify all contributors at least 14 days prior to the PGB vote
- 3-day email call for objections period
- PGB must have Project Approval Minimum Membership
- PSD may be any set of contributions to the project, including from its Releases or Group Releases
- Must conform to the PS template, which includes methods for indicating the relevant designated branches and applicable licenses

#### Project Specification Draft (PSD) -> Project Specification (PS)

A PGB having at least Project Approval Minimum Membership may act to approve any Project Specification Draft as a Project Specification, by satisfying each of the following requirements:

- 14-day written notice of PS nomination must be given by the PGB to all those involved with NIEM Open and the Open Project Administrator prior to initiating a ballot.
- The ballot must be conducted by a Special Majority Vote of the PGB.
- Any machine-executable instructions in a specific computer language (code) that are included in the Project Specification must be composed only of one or more Releases or Group Releases bearing Implementer-Class Licenses.
- Any guidance, descriptions, processes, models for the behavior of a system or service, or other content that is not machine-executable, and is included in the Project Specification, must be composed only of contributions (which may include Releases or Group Releases) previously made to a Project Repository.
- The proposed Project Specification will be subject to review and confirmation of conformance by the Open Project Administrator before the approval ballot is opened.

#### Project Specification (PS) -> OASIS Standard

- Three Statements of Use referencing the PS must be presented to the PGB.
- PGB having at least Project Approval Minimum Membership may approve the PS as a candidate for OASIS Standard in the same manner, and subject to the same requirements, as apply to Committee Specifications.
- Requirements include PGB Special Majority Vote.
- Subject to distinct licensing terms.
- Upon successfully meeting requirements, the OASIS TC Administrator shall proceed with public review (minimum 60 days) and a call for consent.
  - If no comments are received, the TC Administrator must start the call for consent for OASIS standard (within 7 days of notification).
  - If comments are received but no changes will be made, the Chair will request that the TC Administrator start a Special Majority Ballot for the TC to approve continuing with the OASIS Standard call for consent (ballot must begin within 7 days of receipt). Upon successful completion of that ballot, the TC Administrator will, within 7 days, begin the call for consent for OASIS Standard approval.
  - If comments were received and only Non-Material Changes are to be made to the Committee Specification, the editor(s) may prepare a revised Committee Specification. Changes may only be made to address the comments. The TC must provide an acceptable summary that is clear and comprehensible of the changes made and a statement that the TC judges the changes to be Non-Material to the TC Administrator and request a Special Majority Vote to proceed with the call for consent. The TC Administrator shall hold the Special Majority Vote and announce it to the OASIS membership and optionally on other public mail lists along with the summary of changes and the TC's statement. If the Special Majority Vote passes, the TC Administrator must start the call for consent for OASIS Standard within 7 days of notification.
  - If comments were received and Material Changes are to be made to the Committee Specification, the editors(s) may prepare a revised specification to be approved as a Committee Specification Draft by the TC and proceed with a subsequent Public Review as noted in Section 2.6 (Committee Specification Draft - initial review 30 days, subsequent reviews 15 days). Before resubmission the specification must be approved as a Committee Specification.

### NIEM Open

#### Goals

- [ ] Comply with [OP requirements](https://www.oasis-open.org/policies-guidelines/open-projects-process/#progression-of-project-work) (see sections 11-14) including:
  - [ ] Email the [project distribution list](mailto:niemopen@lists.oasis-open.org) to announce any artifacts that require a vote of the PGB to approve (e.g. release/group releases, PSD, PS) at least 14 days in advance of the vote
  - [ ] Conform PSDs and later to the PS template, which includes methods for indicating the relevant designated branches and applicable licenses
  - [ ] Ensure any code included in a PS is composed only of Releases or Group Releases bearing Implementer-Class Licenses.
  - [ ] Ensure any other content in a PS was contributed to a project repository
- [ ] When possible, combine PGB notifications of related artifacts.
- [ ] Use permanent links to specific commits for approval by the PGB
- [ ] Charter a Harmonization Subcommittee under the NBAC to review and recommend content changes to the model

#### NIEM minor version process

**Designated Branch**

Begin domain and other non-Core related updates to the model

- [ ] Maintainers create a designated branch for a new version 
- [ ] Contributors create pull requests for each issue
  - [ ] Domain Committee accepts changes to changes in their domain
  - [ ] Harmonization Subcommittee accepts changes to
    - [ ] Non-domain code tables, adapters and external standards, Core supplements, etc.
    - [ ] Other content if the authority (e.g. Domain Committee) is unresponsive
    - Harmonization Committee reviews content and accepts changes to harmonize domains and Core

**Release**

All major content changes have been submitted.

- [ ] Contributors continue to create pull requests (as above)
- [ ] TSCs review the designated branch and contribute changes (as needed)
- [ ] TSCs announce on the [project distribution list](mailto:niemopen@lists.oasis-open.org) their recommendation that the PGB approve the designated branch as a release
- [ ] If the PGB approves the release,
  - [ ] Maintainers add the appropriate tag (e.g., niem-6.0-release1) and publish as a Github release
  - [ ] TSCs announce release to the [project distribution list](mailto:niemopen@lists.oasis-open.org) for a 14 day review period
  - [ ] Contributors submit feedback via GitHub issues and pull requests
  - [ ] Maintainers review feedback, continuing to get TSC approval before accepting pull requests
  - [ ] TSCs review the release and request approval of additional releases (as needed)

**Project Specification Draft**

All known changes should be submitted for the version.  Should be finalizing the model and doing a final review for issues.

- [ ] Once the TSCs are satisfied with the release, TSCs announce on the [project distribution list](mailto:niemopen@lists.oasis-open.org) their recommendation that the PGB approve the release as a PSD
- [ ] If the PGB approves the PSD,
  - [ ] Maintainers add the appropriate tag (e.g., `niem-6.0-psd1`) and publish as a Github release
  - [ ] TSCs announce the PSD to the [project distribution list](mailto:niemopen@lists.oasis-open.org) for a 14 day review period
  - [ ] Contributors submit feedback via GitHub issues and pull requests
  - [ ] Maintainers review feedback, continuing to get TSC approval before accepting pull requests
  - [ ] TSCs review the release and request approval of additional PSDs (as needed)

**Project Specification**

- [ ] Once the TSCs are satisfied with the PSD, TSCs announce on the [project distribution list](mailto:niemopen@lists.oasis-open.org) their recommendation that the PGB approve the PSD as a PS
- [ ] If the PGB approves the PS (by Special Majority Vote),
  - [ ] Maintainers add the appropriate tag (e.g., `niem-6.0-ps1`) and publish as a Github release
  - [ ] TSCs announce the PS to the [project distribution list](mailto:niemopen@lists.oasis-open.org)
  - [ ] Open Project Administrators publish the PS on https://docs.oasis-open.org/niem

#### NIEM major version process

The process will be similar to NIEM minor versions, with the following key differences.

**Designated Branch**

- [ ] NBAC Harmonization Subcommittee reviews Core and Core-related pull requests
- [ ] NBAC Harmonization Subcommittee meets regularly for harmonization work
- [ ] NTAC reviews architectural-related pull requests for the model
- [ ] NTAC works on specification updates
