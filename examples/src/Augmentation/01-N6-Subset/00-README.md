* Created *n6sub.cmf* with `cmftool n5to6 ../00-N5-Subset/n5sub.cmf`
* Edited *n6sub.cmf* to
  * Fix the JXDM version number; cmftool isn't smart enough to do that
  * Change the conformance targets to `SubsetSchemaDocument` (there are no documentation elements, etc.)
  * Put "subset" into the `SchemaVersionText` properties.
* Created the XSD files with `cmftool m2xsrc n6sub.cmf`

