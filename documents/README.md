# [<img src="https://github.com/niemopen/oasis-open-project/raw/main/artwork/NIEM-NO-Logo-v5.png" alt="img" style="zoom: 10%;" />](https://github.com/niemopen/oasis-open-project/blob/main/artwork/NIEM-NO-Logo-v5.png)

# NIEM Technical Architecture Committee (NTAC)

## NTAC discussion papers

These documents are for NTAC discussion.  They are not expected to become project specifications.  Ideas in these papers are not always implemented in NIEM 6.  Older papers are often contradicted by later papers.  Some of these papers also appear in NTAC github repo [discussions](https://github.com/niemopen/ntac-admin/discussions) or [issues](https://github.com/niemopen/ntac-admin/issues).

Discussion of documents intended to become project specifications are also conducted on the [discussion tab](https://github.com/niemopen/ntac-admin/discussions).

* [Self-describing messages (2025-06-16)](docs/SelfDescribing-250616.md)
  Conventions to make NIEM messages contain URIs for their message format, type, a
* [Cardinality constraints for properties (2025-05-26)](docs/PropertyConstraints-250526.md)
  Probably need these since we replaced RoleOf with structures:uri
* [Revisiting URIs and IDs and REFs (oh my!) (2025-05-26)](docs/References-250526.md)
  Constraints on object references
* [Literal properties or structured datatypes (2024-11-25)](docs/StructuredDatatype-241125.md)
  An argument giving six reasons why literal properties are better
* [Literal properties and atomic classes in NIEM 6 (2024-10-15)](docs/Literals-241015.pdf)
  RDF-star can't replace literal properties, alas.
* [Literal properties and atomic classes in NIEM 6 (2024-10-09)](docs/Literals-241009.md)
  Necessary to keep XML weirdness out of JSON, RDF, and everything else.
* [Implementing augmentations in NIEM 6 (2024-10-03)](docs/AttributeAugmentation-241003.md)
  We don't need attribute wildcards in structures after all.  **But we will have them**
* [New appinfo for attribute augmentation (2024-01-09)](docs/AugAppinfo-240109.md)
  We might still need something like this.
* [Configuration management in NIEM 6: CMF and XSD files (2024-01-08)](docs/CM-CMF-XSD-240108.md)
  Need incomplete CMF model files as equivalents to schema documents.
* [Namespace URIs (2023-07-27)](docs/NamespaceURIs-230727.md)
  NIEM namespace URIs should end in /, not #.
* [NIEM models in XSD and CMF (2023-07-17)](docs/Models-XSD-CMF-230717.md)
  A taxonomy of models and schemas. **OBE**
* [XSD conformance targets in NIEM 6 (2023-07-11)](docs/ConformanceTargets-230711.md)
  Introduces *message model* vs. *reuse model*.
* [URIs and IDs and REFs, Oh My! (2023-06-30)](docs/ReferenceAtts-230630.md)
  Reference attributes can be schema valid on some types/elements and not others.
* [Overview of NIEM 6 architecture changes (2023-06-10)](docs/NIEM6-230610.md)
  Thirteen changes described.
* [RoleOf properties (2023-05-02)](docs/RoleOfProperties-230502.md)
  Worked examples of role properties replaced with structures:uri.
* [NIEM 6 digital engineering (2023-03-14)](docs/DigitalEngineering-230314.md)
  Thoughts about software build automation for complex message specifications.
* [Augmentation points are necessary (2023-02-23)](docs/AugmentationPointsNecessary-230223.md)
  Can't do element augmentation with wildcards after all.
* [Message extension in NIEM 6 (2023-02-20)](docs/MessageExtension-230220.md)
  Is the extended ERNIE message a valid BURT message, or not?
* [Augmentations and metadata in NIEM 6 (2023-02-19)](docs/AugmentationsAndMetadata-230219.md)
  How to augment simple content with an object property.
* [Wildcard augmentations in NIEM 6 (2023-02-03)](docs/WildcardAugmentations-230203.md)
  We didn't go with this proposal. Wouldn't have worked anyway.
* [Attribute augmentations and IC-ISM in NIEM 6 (2023-01-18)](docs/AttributeAugmentations-230118.md)
  Fix ugly JSON and the IC-ISM hack!
* [Relationship properties for IC-ISM in NIEM 6 (2023-01-17)](docs/RelationshipProperties-230117.md)
  Not trying to fix ISM for the IC... but they might want this.
* [NIEM 5.2 and OASIS Options (2023-01-17)](docs/oasis-niem-5.2-release-issues.md)
  COAs for NIEM 5.2 in NIEMOpen.
* [NIEMOpen deliverable strategy (2023-01-09)](docs/niemopen-deliverable-strategy.md)
  * [Jim Cabral edits](docs/niemopen-deliverable-strategy-jec.md)
* [NIEM 6.0 Technical Architecture: What's New? [2022-12-08]](docs/NewInNIEM6-221205.pdf)
  Briefing.
* [Simple content and literal properties (2022-11-24)](docs/Literals-221124.md)
  More on literal properties, with examples.
* [Using xsi:type instead of substitution (2022-11-16)](docs/XSI-Type-221116.md)
  Why it won't work.
* [Metadata adjustments [2022-07-12]](docs/metadata-adjustments.md)
  NDR and content changes for NIEM 6.0 if metadata mechanism is removed.
* [NIEM message specifications (2022-08-02)](docs/N6MsgSpec-220802.docx)
  Some brainstorming about the manifest simple and complex specifications.
* [RDF interpretations of structures:uri and structures:id (2022-06-30)](docs/URIs-220630.md)
  Depends on whether the message has a base URI.
* [Internationalization scenarios for NIEM (2022-06-28)](docs/I18n-220628.pdf)
  I18n scenarios, with examples, and NTAC advice.
* [CMF and RDF representation in NIEM 6 [2022-06-10]](docs/NIEM6-RDF-220610.md)
  Changes to NIEM 5; mapping patterns between XML, JSON, RDF.
* [Metadata mechanism options (2022-05-23)](docs/MetaMechOptions-220523.md)
  Sometimes metadata is a child element, sometimes not.
* [Metadata: Back to basics (2022-05-12)](docs/MetadataBasics-220512.md)
  Reasons for the NIEM 5 metadata mechansim considered.
* [Relationship metadata, revisited [2022-05-09]](docs/RelMetadataAgain-220509.md)
  Looks like RDF-star would work for this!
* [Semantic attributes and metadata in NIEM 6 (2022-04-04)](docs/AttributesAndMetadata-220404.docx)
  Introducing literal properties for attributes on simple content.
* [Metadata and augmentations (2022-03-21)](docs/MetadataAndAugmentations-220321.docx)
  Why not replace the metadata mechanism with augmentation?
* [NIEM schema documents: Reference, extension, subset, constraint (2021-12-01)](docs/SchemaDocs-211201.docx)
  What are these things?
* [Simplified NIEM XML (2021-11-15)](docs/SimpleNIEM-211115.docx)
  Developer gripes, plus Ways to make developers happier.
* [The NIEM metamodel and Common Model Format (2021-11-07)](docs/Terminology-211007.docx)
  Runtime data, development-time data models, and the metamodel, explained!
* [Relationship metadata in NIEM (2021-08-20)](docs/MetadataRepresentation-210820.docx)
  Ugly JSON, relationship types? (Outdated; RDF*)
* [Equivalent message serializations in NIEM (2021-08-18)](docs/EquivalentMessages-210818.docx)
  Message element not needed in JSON? Self-describing data.
* [Ideas for NIEM 6 NDR (2021-08-09)](docs/NDR6Notes-210809.docx)
  Replace sequencID, deprecate semantic attributes, allow elements with simple type, allow external simple elements w/o adapter type, rules for namespace URIs.