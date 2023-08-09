* Copied the XSD from 01-N6-subset
  * *adapters, utility,* and *niem-core.xsd*
  * Removed *domains/jxdm.xsd*, because this specification does not use Justice augmentations

* Added *message-model.xsd*

* Created *noaug.cmf* with `cmftool x2m -o noaug.cmf message-model.cmf`

* Generated XSD with `cmftool m2xmsg -o tmp noaug.cmf`

  * Then replaced the XSD with the generated XSD in *tmp*
  * Observe that the *adapters* directory goes away.  Message schema documents do not need proxy types.

* Generated *noaug.cmf* from this new XSD with `cmftool x2m -o noaug.cmf message-model.xsd`

  