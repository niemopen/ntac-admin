
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
  - [ ] permit changes to Core, domains, code tables, and any other namespace
  - [ ] permit architectural changes to the model, including changes to utility schemas, typically specified by the NDR.
  - [ ] will progress to OASIS standard and be submitted for ISO standard
- [ ] Minor versions of the NIEM model
  - [ ] will progress to OASIS project specifications (PS)
  - [ ] New: consider allowing changes to Core in minor versions, with guidance to hold big changes to existing content to the next major version
  - [ ] specifications are typically not updated, but can be if needed if they do not impact the model.
- [ ] NIEM supplements (Core, domain, etc.) can be published outside of the NIEM release cycle to additively provide new or updated content.

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

- [ ] Use https://docs.oasis-open.org instead of https://release.niem.gov and https://reference.niem.gov/niem/specifications
  - [ ] Need to update target namespaces of the release schemas
  - [ ] Need to update conformance target identifiers of the specifications
- [ ] Continue to maintain release.niem.gov, etc., so the older URIs still resolve.
- [ ] Archive the NIEM Classic NIEM-Releases and other specification repos.  Content will still be available, but changes and new issues would no longer be able to be posted.
- [ ] New NIEM Open GitHub repos
  - [ ] separate repos
    - [ ] https://github.com/niemopen/niem-models (make it singular?)
    - [ ] https://github.com/niemopen/niem-naming-design-rules
    - [ ] NTAC owns spec repos, NBAC owns model
    - [ ] users can subscribe to the individual ones they are interested in
    - [ ] final products will be consolidated at docs.oasis-open.org
    - [ ] can update products individually (e.g., Code Lists 4.0.1)
    - [ ] can publish drafts for review on separate schedules
  - [ ] combined repo
    - [ ] https://github.com/niemopen/niem
    - [ ] single place for model, specs, issues
    - [ ] single deliverable schedule (e.g., beta1/psd1 for the model, NDR, Message Spec, etc.)
    - [ ] an update to one product would bump the version to the whole set
    - [ ] can't subscribe to only the products users are interested in
  - [ ] Branching
    - [ ] `main` branch for final deliverable products
    - [ ] `dev` branch for incremental changes
    - [ ] ad hoc feature branches as needed
- [ ] Tags to identify drafts for review and OASIS deliverables.
  - [ ] Use a consistent tag for all prior NIEM Classic releases and future NIEM Open model versions (`niem-*` or `model-*`).
    - Consistent pattern makes websites, scripts, etc., that reference these things easier
  - [ ] Add new tags for PSD, PS, and OASIS standards
    - These will identify NIEM Open-only products.
    - e.g., `niem-6.0-psd1`, `niem-6.0-ps`, `niem-6.0-os` (or `niemopen-6.0-psd1`, etc.)
  - [ ] Tag bases
    - [ ] continue pattern of NIEM Classic?  `niem-*` or `niemopen-*` for the model, specification abbreviations for the specs (e.g., `ndr-*`)?
      - [ ] `niemopen-*` could be canonical, `niem-*` could be additional for consistency with older classic versions
    - [ ] use `model-*` for versions of the model and specification abbreviations for the specs? (e.g., `ndr-*`)
    - [ ] use the same tag base for all products (e.g., `niem-*`, `niemopen-*` or `v*`)
- [ ] Commit previous versions of the model and specifications from NIEM Classic to `main` as general resources (NIEM 1.0 - NIEM 5.2) before committing work on NIEM 6.0.
  - allows users to access any NIEM release / version of the model from a single repo
  - need to commit releases in order so newer releases are not overwritten by older releases (e.g., commit 1.0 - 5.1 before committing 5.2)
  - allows users to diff any two NIEM releases, regardless of whether or not they fall under NIEM Classic or NIEM Open.  Cannot do this if releases are not in the same repo.
  - commit/contribute only the actual releases instead of the full commit history to reduce clutter (full commit history still available on archived NIEM Classic repos)
  - commit CTAS 3.0 before committing the OASIS formatting updates
- [ ] OASIS PSD / PS requirements
  - [ ] specifications require OASIS template formatting and license
  - [ ] model only requires license

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

- [ ] Limit the number of preliminary OASIS deliverables (releases, group releases, PSDs) because each requires roughly 3 weeks of lead time:
  - a 14-day notice to the PGB
  - a 3-day window for an email call for objection if not resolved over a PGB meeting
- [ ] Continue holding public reviews of the model and specifications before we get to the 60-day OASIS standard review.
- [ ] Schemas and specification rules should not change between a Project Specification and an OASIS standard.  Use supplements if needed to address material changes.
- [ ] Batch together some of the PGB notifications.  Do not want to have to hold separate PGB notifications and calls for objections for each deliverable of the model and each specification.
- [ ] Set up a Content Subcommittee (or Harmonization Working Group) under the NBAC to review and approve content changes to the model
- [ ] Use tags as a permanent link to specific commits for review by the PGB

#### NIEM minor version process

**alpha**

Begin domain and other non-Core related updates to the model

- [ ] Replace NIEM classic alpha with individually-reviewed pull requests
  - [ ] Create separate pull requests per issue / change request
  - [ ] Domain modeler reviews and approves the pull request for their domain
  - [ ] Content Subcommittee reviews and approves pull request for non-domain code tables, adapters and external standards, Core supplements, etc.
- [ ] Fall back to review and approval by the Content Subcommittee if an issue contributor or other authoritative source is unresponsive

**beta**

All major content changes have been submitted by domains.

- [ ] Update non-domain code tables
- [ ] Harmonize if applicable.  More effective if we can update Core in minor versions.
- [ ] Pull requests continue to be approved by contributors and the Content Subcommittee
- [ ] Tag the commit as niem-#.#-beta1
- [ ] Submit the beta tag for release (or PSD) to the PGB for a 14-day notification period
- [ ] Hold a 3-day PGB email call for objections on the release (or PSD)
- [ ] Once approved as an OASIS release (or PSD), add the appropriate tag (niem-#.#-psd1)
- [ ] Publish on GitHub as a draft GitHub release
- [ ] Announce beta / PSD1 to the NIEM community mailing list for a 2-week review period
- [ ] Collect feedback via GitHub issues

**release candidate**

All known changes should be submitted for the version.  Should be finalizing the model and doing a final review for issues.

- [ ] Review and incorporate beta feedback, continuing to get approval before accepting pull requests
- [ ] Tag the commit as niem-#.#-rc1
- [ ] Submit the rc1 tag for PSD to the PGB for a 14-day notification period
- [ ] Hold a 3-day PGB email call for objections on the PSD
- [ ] Once approved as a PSD, add new tag (niem-#.#-psd2)
- [ ] Publish on GitHub as a draft GitHub release
- [ ] Announce the release candidate / OASIS PSD to the NIEM community mailing list for a 1-week review period
- [ ] Collect feedback via GitHub issues
- [ ] Iterate until there are no more comments
  - [ ] Review and apply changes
  - [ ] Get approval for pull request
  - [ ] Merge changes
- [ ] Another full PSD iteration (another 3 weeks) if there are changes to rc1
  - [ ] Tag latest commit as niem-#.#-rc2
  - [ ] Submit rc2 tag to PGB as PSD 3  for 14-day notification period
  - [ ] Hold a 3-day PGB email call for objections on the PSD
  - [ ] Once approved, add new tag (niem-#.#-psd3)

**release**

- [ ] Submit the latest PSD tag as PS to the PGB for a 14-day notification period
- [ ] Hold a PGB Special Majority Vote (1 week for the ballot?)
- [ ] Resolve any PGB issues
- [ ] Tag the commit as niem-#.#-ps
- [ ] Update NIEM tools
- [ ] Publish on GitHub as a GitHub release
- [ ] Update websites, including docs.oasis-open.org (do drafts get posted there as well?)
- [ ] Announce new minor version / PS to the NIEM community

#### NIEM major version process

The process will be similar to NIEM minor versions, with the following key differences.

**alpha**

- [ ] NBAC Content Subcommittee reviews Core and Core-related pull requests
- [ ] NBAC Content Subcommittee meets regularly for harmonization work during alpha and beta
- [ ] NTAC reviews architectural-related pull requests for the model
- [ ] NTAC works on specification updates

**OASIS standard**

A NIEM OASIS standard cannot be scheduled on a strict timeline since it requires three use statements.  Moving a major version of NIEM from a Project Specification to an OASIS standard, and later to an ISO standard, will occur outside of the standard 3-year deliverable cycle.

**ISO standard**

TBD

## Schema versioning

### NIEM Classic

- All schema versions are updated to the release version for major releases
- Only schemas being updated in a minor release (either through content changes or dependency updates) will have the minor release version.
- The Justice domain version is two major version numbers ahead of the NIEM release, permitted via exception for historical reasons
- Model schemas use http://release.niem.gov as the base for target namespaces
- Specifications use http://reference.niem.gov as the base for their identifiers

### NIEM Open

- [ ] Keep uris during minor releases in sync with the actual release number for simplicity / maintainability?
- [ ] Resync Justice version numbers?  Can be confusing to new NIEM users and IEPD developers.
- [ ] Model schemas use https://docs.oasis-open.org/ns/niem/model (or something similar) as base for target namespaces
- [ ] Specifications use https://docs.oasis-open.org/ns/niem/specifications (or something similar) as base for conformance target identifiers
