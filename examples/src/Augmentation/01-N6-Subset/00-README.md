## NIEM 6.0 subset schema with no augmentations

I ran *n5sub.cmf* through `cmftool n5to6`to convert the model to NIEM 6 namespaces.  It's not perfect but it's a lot faster than editing all the namespace URIs by hand.  Then I edited the resulting *subset.cmf* to

* Fix the Justice schema document name and version number; cmftool isn't smart enough to do that
* Change the conformance targets to `SubsetSchemaDocument` (there are no documentation elements, etc.)
* Put "subset" into the `SchemaVersionText` properties.

I ran *subset.cmf* through `cmftool m2xs` to generate the NIEM 6 XSD *subset schema*.  A source schema is composed of reference, extension, and subset schema documents.  The attribute wildcards in *structures.xsd* and *niem-xs.xsd* are what make this a subset schema.  
