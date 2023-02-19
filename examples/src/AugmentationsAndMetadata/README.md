This directory contains example schema documents and messages to demonstrate

* wildcard attribute augmentations
* wildcard element augmentations
* reference attribute augmentations
* replacing metadata mechanism with augmentation

Directory *referenceSchema* shows the XSD for the NIEM reference model.  It shows what the reference schema documents would look like in the proposed NIEM 6.  The core and domain reference schema documents would actually have definitions for the full core and domain models.  You should pretend the missing definitions are there, looking like the examples you see.  The *messageModel.xsd* document isn't part of the NIEM reference model, of course; it's here for validation convenience.

Directory *augmentation* contains the message schema created when the message designer says "augment `nc:EducationType` with `em:EducationMinorText`, `j:EducationTotalYearsText`, and `nc:CommentText`".  It also contains a sample message.  Observe that *sample1-valid.xml* is valid when assessed against the reference schema, so the subset rule is satisfied.  Observe that *sample2-invalid.xml* is valid against the reference schema but not valid against the message schema, so the reference schema wildcards are not permitting arbitrary content in a message.  This directory illustrates the proposed alternative to the exisiting augmentation approach.

Directory *augWithAttribute* contains the message schema created when the message designer says "augment `j:EducationTotalYearsText` with `my:kindOfYears`".  It shows how to augment simple content with arbitrary attributes.

Directory *mdObjectOnComplex* contains the message created when the message designer says "augment `nc:EducationType` with the `my:CategoryMetadata` object property".  Note that `my:CategoryMetadata` is an ordinary object; its nature as metadata is a matter of documentation only; there is no longer a`structures:MetadataType` to make it special.  It's just another augmentation.  The *sample1-valid.ttl* file shows the RDF interpretation of this message.  It's the same RDF we would get by using `structures:metadata`.

Directory *mdAttributeOnBoth* contains the message created when the designer says "augment `nc:EducationType` and `nc:EducationInProgressIndicator` with the`my:privacyCode` data property, which in XML is an attribute".  This property has `appinfo:relationshipProperty="true"`, which means it is interpreted as relationship metadata.  The *sample1-valid.ttl* shows the RDF-star equivalent.

Directory *mdObjectOnSimple* contains the message created when the designer says "augment `nc:EducationType` (complex content) and `j:EducationTotalYearsText` (simple content) with `my:CategoryMetadata` (an object property)."  This can only be done with an attribute value that is a reference to the augmenting object.  But instead of using `structures:metadata` for this, we augment with a special `my:categoryMetadataRef` attribute.  We also use `structures:augID` instead of `structures:id` for objects that don't apply to their parent element.



